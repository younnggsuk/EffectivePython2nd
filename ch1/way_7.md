# 7. range보다는 enumerate를 사용하라

## TL;DR

- sequence의 원소와 원소의 index를 얻는 코드를 작성할 때, range보다는 enumerate를 사용하는 것이 코드를 더 간결하고 깔끔하게 작성할 수 있다.
- `enumerate`는 iterator를 lazy generator(지연 계산 제너레이터)로 감싸서 루프 index와 원소로 이루어진 튜플을 넘겨주므로, 한번에 큰 메모리를 사용하지 않고 아주 긴 입력도 처리할 수 있게 해준다.
- `enumerate`의 두 번째 파라미터로 시작 index를 지정할 수 있다.



## `range`를 이용한 원소 접근

다음은 `range`를 사용해 sequence의 원소에 접근하는 코드이다.

```python
flavor_list = ['바닐라', '초콜릿', '피칸', '딸기']

for i in range(len(flavor_list)):
    flavor = flavor_list[i]
    print(f"{i + 1}: {flavor}")
    
>>>
1: 바닐라
2: 초콜릿
3: 피칸
4: 딸기
```

`range`는 list의 길이를 알아내고, 알아낸 인덱스를 사용해 다시 list의 원소에 접근하는 과정을 거치므로, 가독성이 좋지 못하고 코드를 보고 바로 이해하기 어렵다.



##  `enumerate`를 이용한 원소 접근

다음은 `enumerate`를 사용해 sequence의 원소에 접근하는 코드이다.

```python
flavor_list = ['바닐라', '초콜릿', '피칸', '딸기']

for i, flavor in enumerate(flavor_list):
    print(f"{i + 1}: {flavor}")

>>>
1: 바닐라
2: 초콜릿
3: 피칸
4: 딸기
```

`enumerate`는 for문에서 index와 원소를 간결하게 언패킹하여서 사용하기 때문에, 코드가 간결하고 가독성이 좋다.

따라서, sequence의 원소와 원소의 index에 접근할 때에는 `enumerate`를 사용해 더욱 간결하고 깔끔하게 코드를 작성하는 것이 좋다.

### lazy generator

`enumerate`는 iterator를 lazy generator로 감싸고, 이 generator는 각 이터레이터의 다음 값이 들어있는 튜플을 반환한다. 이 튜플에는 루프 index와 원소가 들어있으며, for문에서 바로 언패킹하여 사용할 수 있다.

또한, `enumerate`는 자신이 감싼 이터레이터 원소를 하나씩 소비하므로, 메모리를 한번에 소모해서 프로그램이 중단되는 위험 없이 아주 긴 입력도 처리할 수 있게 해준다는 장점이 있다.

다음은 `enumerate`가 반환한 iterator가 어떻게 동작하는지를 보여주는 코드이다.

```python
flavor_list = ['바닐라', '초콜릿', '피칸', '딸기']

it = enumerate(flavor_list)

print(next(it))
print(next(it))

>>>
(0, '바닐라')
(1, '초콜릿')
```

### enumerate의 시작 index 설정

`enumerate`의 두 번째 파라미터로 index를 몇번부터 셀 지를 지정할 수 있다. (default는 0)

다음은 index를 1부터 세도록 지정하여, 앞의 코드에서 `i + 1`로 출력한 부분을 `i`로 변경한 코드이다.

```python
flavor_list = ['바닐라', '초콜릿', '피칸', '딸기']

for i, flavor in enumerate(flavor_list, 1):
    print(f"{i}: {flavor}")

>>>
1: 바닐라
2: 초콜릿
3: 피칸
4: 딸기
```

