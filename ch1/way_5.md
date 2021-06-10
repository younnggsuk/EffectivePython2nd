# 5. 복잡한 식을 쓰는 대신 도우미 함수를 작성하라

## TL;DR

- 파이썬 문법을 사용하면 코드를 간결하게 작성할 수 있지만, 코드가 길어지더라도 의미를 파악하기 좋게 하는 것이 더 좋다.
- 식이 복잡해지고, 자주 반복된다면 helper function을 사용하는 것이 좋다.
- or 또는 and 연산자를 통한 가독성이 떨어지는 한 줄짜리 코드보다 if/else를 사용한 가독성이 높은 여러 줄짜리 코드가 더 좋다.



## 파이썬 문법의 간결함

다음과 같은 URL의 query string를 parsing하는 코드가 있다고 하자.

```python
from urllib.parse import parse_qs

my_values = parse_qs("빨강=5&파랑=0&초록=",
                     keep_blank_values=True)


print(repr(my_values)) # my_values 출력
print('빨강: ', my_values.get('빨강')) # 값이 있는 경우
print('초록: ', my_values.get('초록')) # 값이 없는 경우
print('투명도: ', my_values.get('투명도')) # key가 아예 없는 경우

>>>
{'빨강': ['5'], '파랑': ['0'], '초록': ['']}
빨강:  ['5']
초록:  ['']
투명도:  None
```

위 결과에서, `get()` method를 이용해 초록, 투명도에 대한 값을 default로 항상 `int` 0을 받고싶다고 하자.

이러한 로직을 짤 때, 파이썬은 다음의 문법을 이용하여 다음과 같이 한 줄짜리 코드로 간결하게 작성할 수 있다.

- if/else 조건식(삼항 조건식)을 사용
- 파이썬은 빈 문자열, 빈 list, 0이 모두 False로 평가
- 왼쪽의 식이 `False`인 경우 모두 or 연산자의 오른쪽 식으로 계산

```python
from urllib.parse import parse_qs

my_values = parse_qs("빨강=5&파랑=0&초록=",
                     keep_blank_values=True)

red_str = my_values.get('빨강', [''])
red = int(red_str[0]) if red_str[0] else 0

green_str = my_values.get('파랑', [''])
green = int(green_str[0]) if green_str[0] else 0

opacity_str = my_values.get('투명도', [''])
opacity = int(opacity_str[0]) if opacity_str[0] else 0

print(f'빨강: {red!r}')
print(f'초록: {green!r}')
print(f'투명도: {opacity!r}')

>>>
빨강: 5
초록: 0
투명도: 0
```



## 간결함보다는 가독성

그런데, 가독성 측면에서는 위의 간결한 코드보다 로직을 분리해서 다음과 같이 작성하는 것이 더 좋다.

```python
from urllib.parse import parse_qs

my_values = parse_qs("빨강=5&파랑=0&초록=",
                     keep_blank_values=True)

red_str = my_values.get('빨강', [''])
if red_str[0]:
    red = int(red_str[0])
else:
    red = 0

green_str = my_values.get('파랑', [''])
if green_str[0]:
    green = int(green_str[0])
else:
    green = 0

opacity_str = my_values.get('투명도', [''])
if opacity_str[0]:
    opacity = int(opacity_str[0])
else:
    opacity = 0

print(f'빨강: {red!r}')
print(f'초록: {green!r}')
print(f'투명도: {opacity!r}')

>>>
빨강: 5
초록: 0
투명도: 0
```



## 반복된 코드는 Helper function으로

코드가 어떤 의미인지는 더 파악하기 좋아졌지만, 이 경우에는 복잡한 로직이 여러번 반복되어 코드가 길어진다는 단점이 있다.

이러한 경우에는 helper function을 사용하면 다음과 같이 가독성 측면에서도 좋고 반복을 줄이는 형태로 작성할 수 있다.

```python
from urllib.parse import parse_qs


def get_first_int(values, key, default=0):
    found = values.get(key, [''])
    if found[0]:
        return int (found[0])
    return default


my_values = parse_qs("빨강=5&파랑=0&초록=",
                     keep_blank_values=True)

red = get_first_int(my_values, "빨강")
green = get_first_int(my_values, "초록")
opacity = get_first_int(my_values, "투명도")

print(f'빨강: {red!r}')
print(f'초록: {green!r}')
print(f'투명도: {opacity!r}')

>>>
빨강: 5
초록: 0
투명도: 0
```

따라서, 이번 챕터의 내용을 요약하면 다음과 같다.

- 파이썬 문법을 사용하면 코드를 간결하게 작성할 수 있지만, 코드가 길어지더라도 의미를 파악하기 좋게 하는 것이 더 좋다.
- 반복되는 복잡한 식의 경우, helper function으로 만들면 간결하면서도 의미를 파악하기 좋은 코드를 작성할 수 있다.
