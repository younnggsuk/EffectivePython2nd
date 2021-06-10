# 3. `bytes`와 `str`의 차이를 알아두라

## TL;DR

- `bytes`에는 8bit의 시퀀스, `str`에는 유니코드 코드 포인트의 시퀀스가 들어있다.
- helper function을 사용해 처리할 입력이 원하는 문자 시퀀스인지 확실히 하는 것이 좋다.
- `open()`을 사용해 파일을 읽고 쓸 때에는, 데이터가 바이트열인지 유니코드 문자열인지에 따라서 `mode` 매개변수를 잘 설정해주어야 한다.
- 유니코드 데이터를 읽고 쓸 경우에는 시스템 기본 인코딩 방식에 유의해야 하며, 기본 인코딩 방식이 의심스러운 경우에는 `encoding` 매개변수로 인코딩 방식을 명시적으로 전달하는 것이 안전하다.



## `bytes`와 `str`

파이썬의 문자열 시퀀스 표현을 위한 자료형은 `bytes`와 `str`이 있다.

`bytes`는 8 bit(1 byte) 값의 시퀀스가 들어있으며, 파이썬 코드 예는 다음과 같다.

```python
a = b'h\x65llo'
print(list(a))
print(a)

>>>
b'hello'
[104, 101, 108, 108, 111]
```

`str`에는 유니코드 code point의 시퀀스가 들어있으며, 파이썬 코드 예는 다음과 같다.

```python
a = 'a\u0300 propos'
print(list(a))
print(a)

>>>
['a', '̀', ' ', 'p', 'r', 'o', 'p', 'o', 's']
à propos
```



## `encode()`와 `decode()`

`str`과 `bytes`는 `encode()`와 `decode()`를 통해 서로 변환이 가능하며, 처리할 입력이 원하는 문자 시퀀스인지 확실히 하기 위해 helper function을 사용하는 것이 좋다.

바이트열을 유니코드 문자열로 변환하기 위해서는 `bytes.decode()`를 사용한다.

다음은 `bytes`나 `str` 인스턴스를 받아서 항상 `bytes`를 반환하는 helper function의 예이다.

```python
def to_str(bytes_or_str):
    if isinstance(bytes_or_str, bytes):
        # utf-8로 인코딩되었다고 가정
        value = bytes_or_str.decode("utf-8")
    else:
        value = bytes_or_str
    return value

print(repr(to_str(b'foo')))
print(repr(to_str('bar')))
print(repr(to_str(b'\xed\x95\x9c\xea\xb8\x80')))

>>>
'foo'
'bar'
'한글'
```

유니코드 문자열을 바이트열로 변환하기 위해서는 `str.encode()`를 사용한다.

다음은 `bytes`나 `str` 인스턴스를 받아서 항상 `str`을 반환하는 helper function의 예이다.

```python
def to_bytes(bytes_or_str):
    if isinstance(bytes_or_str, str):
        # 인코딩 방식은 utf-8이라고 가정
        value = bytes_or_str.encode('utf-8')
    else:
        value = bytes_or_str
    return value

print(repr(to_bytes(b'foo')))
print(repr(to_bytes('bar')))
print(repr(to_bytes('한글')))

>>>
b'foo'
b'bar'
b'\xed\x95\x9c\xea\xb8\x80'
```



## 읽고 쓰기

파이썬의 내장함수 `open`은 default로 유니코드 문자열을 요구하기 때문에, 이를 고려해서 `mode` 매개변수를 잘 전달해야 한다. 

바이트 문자열을 읽고 쓸 경우에는 `wb`와 `rb`를 사용한다.

```python
with open('data.bin', 'wb') as f:
    f.write(b'\xf1\xf2\xf3\xf4\xf5')

with open('data.bin', 'rb') as f:
    data = f.read()
    print(data)

>>>
b'\xf1\xf2\xf3\xf4\xf5'
```

유니코드 문자열을 읽고 쓸 경우에는 `w`와 `r`을 사용한다.

```python
with open('data.bin', 'w') as f:
    f.write("한글")

with open('data.bin', 'r') as f:
    data = f.read()
    print(data)
  
>>>
한글
```



## 시스템 기본 인코딩 방식

`open`으로 유니코드 문자열을 읽을 경우, 인코딩 방식은 `encoding` 매개변수로 지정할 수 있으며 defalut는 시스템 인코딩 방식을 따른다.

시스템 기본 인코딩 방식 확인 방법은 다음과 같다.

```python
import locale

print(locale.getpreferredencoding())

>>>
UTF-8
```

이때 주의할 점은 시스템 기본 인코딩 방식을 고려하지 않을 경우, 다음과 같은 모호한 문제가 발생할 수 있다는 것이다.

```python
# 윈도우에서 사용하던 legacy 인코딩 방식인 CP-1252로 인코딩된 바이트 문자열을 encoding 매개변수 없이 읽을 경우
with open('data.bin', 'wb') as f:
    f.write(b'\xf1\xf2\xf3\xf4\xf5')

with open('data.bin', 'r') as f:
    data = f.read()
    print(data)

>>> Traceback ...
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xf1 in position 0: invalid continuation byte
```

```python
# 시스템 기본 인코딩 방식인 UTF-8로 인코딩된 바이트 문자열을 encoding 매개변수 없이 바로 읽을 경우
with open('data.bin', 'wb') as f:
    f.write(b'\xed\x95\x9c\xea\xb8\x80')

with open('data.bin', 'r') as f:
    data = f.read()
    print(data)

>>>
한글
```

따라서, 기본 인코딩 방식이 의심스러운 경우에는 `encoding` 매개변수를 통해 직접 인코딩 방식을 지정해 주는 것이 좋다.

```python
with open('data.bin', 'wb') as f:
    f.write(b'\xf1\xf2\xf3\xf4\xf5')

with open('data.bin', 'r', encoding='cp1252') as f:
    data = f.read()
    print(data)

>>>
ñòóôõ
```

```python
with open('data.bin', 'wb') as f:
    f.write(b'\xed\x95\x9c\xea\xb8\x80')

with open('data.bin', 'r', encoding='utf-8') as f:
    data = f.read()
    print(data)

>>>
한글
```
