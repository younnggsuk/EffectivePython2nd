# 4. C 스타일 형식 문자열을 str.format과 쓰기보다는 f-문자열을 통한 인터폴레이션을 사용하라

## TL;DR

- 파이썬 3.6 버전 이상을 사용한다면, 문자열을 다룰 때 항상 f-string을 사용하는 것이 좋다.



## C style format string의 문제점

C언어의 `printf` 함수에서 비롯된 format string 방법은 파이썬에서 다음과 같이 사용할 수 있다.

```python
a = 0b10111011
b = 0xc5f
print("2진수: %d, 16진수: %d" % (a, b))

>>>
2진수: 187, 16진수: 3167
```

하지만, 이 방법에는 많은 문제점들이 있다.

### 1. 순서를 변경할 때, 실수가 일어나기 쉽다.

다음과 같은 코드가 있다고 하자.

```python
key = 'my_var'
value = 1.234
formatted = "%-10s = %.2f" % (key, value)
print(formatted)

>>>
my_var     = 1.23
```

이때 코드를 변경하다보면, 다음의 2가지 경우에서 실수를 하기 쉽다.

- tuple의 순서 또는 타입을 변경하는 경우
- format string의 순서를 바꾸는 경우

```python
key = 'my_var'
value = 1.234

# tuple의 순서가 반대로 된 경우
reordered_tuple = "%-10s = %.2f" % (value, key)
print(formatted)

>>>
Traceback ...
TypeError: must be real number, not str
```

```python
key = 'my_var'
value = 1.234

# format string의 순서를 바꾼 경우
reordered_string = "%.2f = %-10s" % (key, value)
print(reordered_tuple)

>>>
Traceback ...
TypeError: must be real number, not str
```



### 2. 가독성이 떨어진다.

다음과 같이 코드가 조금만 길어져도 가독성이 떨어지게 된다.

```python
pantry = [
    ("아보카도", 1.25),
    ("바나나", 2.5),
    ("체리", 15),
]

for i, (item, count) in enumerate(pantry):
    print("#%d: %-10s = %d" % (
        i + 1,
        item.title(),
        round(count))
    )

>>>
#1: 아보카도       = 1
#2: 바나나        = 2
#3: 체리         = 15
```



### 3. 같은 값을 반복하기 어렵다.

다음과 같이, 같은 값을 반복해야 하는 경우에는 튜플에서도 같은 값을 반복해야하므로, 코드를 작성하는데 있어 실수하기도 쉽고 귀찮다.

```python
name = "철수"
template = "%s는 음식을 좋아해. %s가 요리하는 모습을 봐요."
formatted = template % (name, name)
print(formatted)

>>>
철수는 음식을 좋아해. 철수가 요리하는 모습을 봐요.
```



## format식에 딕셔너리를 사용한 해결법

% 연산자에서 튜플 대신에 딕셔너리를 사용하게 되면, 문제점 1과 3은 해결할 수 있지만, 2는 여전히 해결하지 못한다.

다음은 순서로 인한 문제점 1을 해결한 코드이다.

```python
key = 'my_var'
value = 1.234

# 딕셔너리로 위치를 지정 가능하므로, 순서로 인한 문제점 1을 해결
formatted = "%(key)-10s = %(value).2f" % {
    "key": key, "value": value
}
print(formatted)

>>>
my_var     = 1.23
```

다음은 반복으로 인한 문제점 3을 해결한 코드이다.

```python
name = "철수"
template = "%(name)s는 음식을 좋아해. %(name)s가 요리하는 모습을 봐요."

# 딕셔너리로 한번만 지정해주면 되므로, 반복으로 인한 문제점 3을 해결
formatted = template % {"name": name}
print(formatted)

>>>
철수는 음식을 좋아해. 철수가 요리하는 모습을 봐요.
```

하지만, 딕셔너리 키와 콜론 연산자가 추가되며 코드가 길어지고, 키에 해당하는 값이 변수에 있는 경우에는 코드가 번잡해지게 된다는 문제점은 남아있다.

다음은 문제점 2의 코드에 튜플 대신 딕셔너리를 사용한 코드이며, 여전히 코드가 긴 것을 알 수 있다.

```python
pantry = [
    ("아보카도", 1.25),
    ("바나나", 2.5),
    ("체리", 15),
]

# 여전히 코드가 길고 가독성이 떨어지므로,문제점 2는 해결하지 못함
for i, (item, count) in enumerate(pantry):
    print("#%(loop)d: %(item)-10s = %(count)d" % {
        "loop": i + 1,
        "item": item.title(),
        "count": round(count)}
    )

>>>
#0: 아보카도       = 1.25
#1: 바나나        = 2.50
#2: 체리         = 15.00
```



##  str.format()

파이썬 3부터 `str` 타입에 추가된 `format` 메서드를 사용하면, 위치 지정자 `{}`와 `:`뒤의 형식 지정자를 통해 C 스타일 format string을 대체할 수 있다. 이는 문제점 1과 3을 해결해준다.

다음은 순서로 인한 문제점 1을 해결한 코드이다.

```python
key = 'my_var'
value = 1.234

# {}안에 argument의 순서를 전달 가능하므로, 순서로 인한 문제점 1을 해결
formatted = "{1} = {0}".format(value, key)
print(formatted)

>>>
my_var = 1.234
```

다음은 반복으로 인한 문제점 3을 해결한 코드이다.

```python
name = "철수"

# 같은 위치 인덱스를 여러 번 사용 가능하므로, 반복으로 인한 문제점 3을 해결
formatted = "{0}는 음식을 좋아해. {0}가 요리하는 모습을 봐요.".format(name)
print(formatted)

>>>
철수는 음식을 좋아해. 철수가 요리하는 모습을 봐요.
```

하지만, `format` 메서드도 여전히 가독성면에서는 좋지 못하다.

다음은 문제점 2의 코드에 `format()`을 적용한 코드이며, 여전히 코드가 긴 것을 알 수 있다.

```python
pantry = [
    ("아보카도", 1.25),
    ("바나나", 2.5),
    ("체리", 15),
]

# 여전히 코드가 길고 가독성이 떨어지므로,문제점 2는 해결하지 못함
for i, (item, count) in enumerate(pantry):
    print("#{}: %{:<10} = {}".format(
        i + 1,
        item.title(),
        round(count))
    )

>>>
#1: %아보카도       = 1
#2: %바나나        = 2
#3: %체리         = 15
```



# f-string

파이썬 3.6부터 추가된 f-string을 사용하면, 앞서 살펴본 모든 문제점들을 해결할 수 있다. 특히, f-string을 사용하게 되면, `format()`을 통해서도 해결하지 못한 코드의 가독성 문제를 해결할 수 있다.

다음은 순서로 인한 문제점 1을 해결한 코드이다.

```python
key = 'my_var'
value = 1.234

formatted = f"{key} = {value}"
print(formatted)

>>>
my_var = 1.234
```

다음은 가독성으로 인한 문제점 2를 해결한 코드이다.

```python
pantry = [
    ("아보카도", 1.25),
    ("바나나", 2.5),
    ("체리", 15),
]

for i, (item, count) in enumerate(pantry):
    print(f"#{i+1}: %{item.title():<10} = {round(count)}")
    
>>>
#1: %아보카도       = 1
#2: %바나나        = 2
#3: %체리         = 15
```

다음은 반복으로 인한 문제점 3을 해결한 코드이다.

```python
name = "철수"

# string에 그냥 변수명을 사용하면 되므로, 반복으로 인한 문제점 3을 해결하면서 가독성도 더 높아짐
formatted = f"{name}는 음식을 좋아해. {name}가 요리하는 모습을 봐요."
print(formatted)

>>>
철수는 음식을 좋아해. 철수가 요리하는 모습을 봐요.
```

추가로, f-string은 다음과 같이 `:`을 이용한 형식 지정자와 `repr` 문자열로 변환하는 기능도 사용할 수 있다.

```python
key = 'my_var'
value = 1.234

formatted = f"{key!r:<10} = {value:.2f}"
print(formatted)

>>>
'my_var'   = 1.23
```

따라서, 파이썬 3.6 버전 이상을 사용한다면 항상 f-string을 사용하는 것이 좋다.

