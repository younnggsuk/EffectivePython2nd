# 6. 인덱스를 사용하는 대신 대입을 사용해 데이터를 언패킹하라

## TL;DR

- 파이썬은 여러 값을 한번에 대입할 수 있는 언패킹이라는 문법을 제공한다.
- 언패킹은 모든 iterable에 적용이 가능하며, 여러 계층으로 내포된 경우에도 적용이 가능하다.
- 시퀀스 내부 원소에 접근할 때에는, 인덱스보다는 언패킹을 사용한 접근이 가독성을 높이고 코드를 더 명확하게 하므로, 언패킹을 사용하자.



## 언패킹 (unpacking)

언패킹 구문을 사용하면, 한 문장 안에서 여러 값을 대입할 수 있다.

다음은 튜플의 원소에 접근하기 위해 인덱스를 사용한 방법과 언패킹을 사용한 방법을 비교한 것이다.

```python
item = ("호박엿", "식혜")

# 인덱스를 이용한 접근 방법
first = item[0]
second = item[1]
print(first, "&", second)

# 언패킹을 이용한 접근 방법 (더 간결함)
first, second = item
print(first, "&", second)

>>>
호박엿 & 식혜
호박엿 & 식혜
```

다음과 같이, 여러 계층으로 iterable이 내포된 경우에도 언패킹을 적용할 수 있다. (다음과 같은 코드도 동작한다는 것을 알고는 있어야 하지만, 이러한 방식으로 작성하는 것이 좋은 방식은 아니다.)

```python
favorite_snacks = {
    '짭조름한 과자': ('프레즐', 100),
    '달콤한 과자': ('쿠키', 180),
    '채소': ('당근', 20),
}

((type1, (name1, cals1)),
 (type2, (name2, cals2)),
 (type3, (name3, cals3))) = favorite_snacks.items()

print(f'제일 좋아하는 {type1}는 {name1}, {cals1} 칼로리입니다.')
print(f'제일 좋아하는 {type2}는 {name2},  {cals2} 칼로리입니다.')
print(f'제일 좋아하는 {type3}는 {name3},  {cals3} 칼로리입니다.')

>>>
제일 좋아하는 짭조름한 과자는 프레즐, 100 칼로리입니다.
제일 좋아하는 달콤한 과자는 쿠키,  180 칼로리입니다.
제일 좋아하는 채소는 당근,  20 칼로리입니다.
```

### Swap

언패킹 구문을 사용하면, 임시 변수 없이 한줄에서 두 인덱스가 가리키는 원소를 swap 할 수 있다.

```python
def bubble_sort(a):
    for _ in range(len(a)):
        for i in range(1, len(a)):
            if a[i] < a[i-1]:
                # temp에 저장해둔 후 swap
                temp = a[i]
                a[i] = a[i-1]
                a[i-1] = temp

names = ['banana', 'apple', 'grape', 'orange']
bubble_sort(names)
print(names)

def bubble_sort(a):
    for _ in range(len(a)):
        for i in range(1, len(a)):
            if a[i] < a[i-1]:
                # temp에 저장하지 않고 한줄로 바로 swap 가능
                a[i-1], a[i] = a[i], a[i-1]


names = ['banana', 'apple', 'grape', 'orange']
bubble_sort(names)
print(names)

>>>
['apple', 'banana', 'grape', 'orange']
['apple', 'banana', 'grape', 'orange']
```

위의 swap 과정은 실제로는 다음의 과정을 통해 수행된다.

1. 대입문 우항 `a[i], a[i-1]`이 계산되어 이름없는 임시 tuple에 저장된다.
2. 대입문 좌항 `a[i-1], a[i]`에 임시 tuple의 값이 저장된다.
3. 임시 tuple의 메모리가 해제된다.

### List의 원소 접근

list의 원소 접근에도 언패킹을 사용하면 코드의 가독성을 더욱 높일 수 있다.

다음은 list의 원소를 인덱스로 접근하는 코드이다.

```python
snacks = [('banana', 350), ('appple', 240), ('orange', 190)]

# index를 이용한 접근 방법
for i in range(len(snacks)):
    item = snacks[i]
    name = item[0]
    calories = item[1]
    print(f'#{i+1}: {name} 은 {calories} 칼로리입니다.')
    
>>>
#1: banana은 350 칼로리입니다.
#2: appple은 240 칼로리입니다.
#3: orange은 190 칼로리입니다.
```

이를 다음과 같이 언패킹을 이용한 접근 방법으로 수정하면, 직접 변수명으로 접근할 수 있기 때문에 더욱 가독성이 좋아진다.

```python
snacks = [('banana', 350), ('appple', 240), ('orange', 190)]

# unpacking을 이용한 접근 방법
for rank, (name, calories) in enumerate(snacks, 1):
    print(f'#{rank}: {name} 은 {calories} 칼로리입니다.')
    
>>>
#1: banana은 350 칼로리입니다.
#2: appple은 240 칼로리입니다.
#3: orange은 190 칼로리입니다.
```

따라서, 위와 같은 루프가 필요할 때에는 언패킹을 사용해 더욱 명확하고 가독성 좋은 코드를 작성하는 것이 좋으며, 이러한 코드가 더욱 pythonic한 코드라고 할 수 있다.
