# 7. range보다는 enumerate를 사용하라

## TL;DR

- sequence의 원소와 원소의 index를 얻는 코드를 작성할 때, range보다는 enumerate를 사용하는 것이 코드를 더 간결하고 깔끔하게 작성할 수 있다.



## range를 이용한 원소 접근

다음은 range를 사용해 sequence의 원소에 접근하는 코드이다.

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

range는 list의 길이를 알아내고, 알아낸 인덱스를 사용해 다시 list의 원소에 접근하는 과정을 거치므로, 가독성이 좋지 못하고 코드를 보고 바로 이해하기 어렵다.



## enumerate를 이용한 원소 접근

다음은 enumerate를 사용해 sequence의 원소에 접근하는 코드이다.

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

enumerate는 for문에서 index와 원소를 간결하게 언패킹하여서 사용하기 때문에, 코드가 간결하고 가독성이 좋다.

따라서, sequence의 원소와 원소의 index에 접근할 때에는 enumerate를 사용해 더욱 간결하고 깔끔하게 코드를 작성하는 것이 좋다.
