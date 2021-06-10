# 2. PEP 8 스타일 가이드를 따르라

## TL;DR

- 파이썬 코드를 작성할 때는 항상 Python Enhancement Proposal #8 (PEP 8) 스타일 가이드를 따르자.



## PEP 8

<a href="https://www.python.org/dev/peps/pep-0008/" target="_blank" rel="noopener noreferrer">Python Enhancement Proposal #8 (PEP 8)</a>은 파이썬 코드 작성 스타일 가이드이다. <a href="https://wikidocs.net/7896" target="_blank" rel="noopener noreferrer">\[PEP8 한국어 번역 위키 문서\]</a>

일관된 스타일을 유지하는 것과 다른 개발자 및 커뮤니티의 공통된 스타일을 공유하면 프로젝트 수행 시, 더 쉽게 협력할 수 있게 해준다. 따라서, PEP 8 스타일 가이드를 따르는 것이 좋다.

다음은 PEP 8의 몇가지의 규칙들이다.

### 공백

- 들여쓰기
  - 4칸 스페이스를 사용한다.
  - 긴 식을 다음 줄에 이어서 쓸 경우에는 4칸 스페이스를 더 들여써서 작성한다.
- 함수 및 클래스
    - 파일 안에서 각 함수 및 클래스 사이에는 빈줄을 2줄 넣는다.
    - 클래스 안에서 메서드와 메서드 사이에는 빈줄을 1줄 넣는다.
- 딕셔너리
    - 키, 콜론 사이에는 공백을 넣지 않는다.
    - 한줄안에 키와 값을 같이 넣는 경우에는 콜론 다음에 스페이스를 1칸 넣는다.
        - `spam(ham[1], {eggs: 2})`
- 변수 대입
    - = 전후에는 스페이스를 하나씩 넣는다.
- 타입 표기를 덧붙이는 경우
  - 변수 이름과 콜론 사이에 공백을 넣지 않는다.
  - 콜론과 타입 정보 사이에는 스페이스를 하나 넣는다.

### 명명 규약

- 함수, 변수, attribute
    - `lowercase_underscore`와 같이 snake case를 사용한다.
- 보호되어야 하는 인스턴스 attribute
    - `_leading_underscore`와 같이 일반적인 attribute 규칙을 따르되 맨앞에 밑줄을 1개 추가한다.
- private 인스턴스 attribute (한 클래스에서만 쓰이고 다른 곳에서는 쓰이면 안되는 경우)
    - `__leading_underscore`와 같이 일반적인 attribute 규칙을 따르되 맨앞에 밑줄을 2개 추가한다.
- 클래스 (Exception도 포함)
    - `CapitalizedWord`와 같이 camel case를 사용한다.
- 모듈 수준의 상수
    - `ALL_CAPS`와 같이 모든 글자가 대문자이고, 단어들을 밑줄로 연결한 형태로 사용한다.

## 식과 문

- 긍정적인 식을 부정하지 말고 부정을 내부에 넣어서 사용한다.
    - `if not a is b` (X)
    - `if a is not b` (O)
- 컨테이너나 시퀀스가 비어있는지 여부를 검사할 때는 0과 비교하지 말고, 암묵적으로 `False`로 취급된다는 사실을 활용해 조건문을 작성한다. (반대로 비어있지 않은 여부를 비교할때에도 `True`로 취급된다는 사실을 활용한다.)
    - `if len(something) == 0)` (X)
    - `if len(something) != 0)` (X)
    - `if not something` (O)
    - `if something` (O)
- 한 줄짜리 `if`, `for`, `while`, `except` 등의 복합문은 명확히 하기 위해 각 부분을 여러 줄에 나눠서 작성한다.
- 식을 한 줄에 다 쓸 수 없는 경우, `\`문자 보다는 괄호를 사용하고 줄바꿈과 들여쓰기를 추가해 가독성 좋게 작성한다.
    ```python
    income = (gross_wages
              + taxable_interest
              + (dividends - qualified_dividends)
              - ira_deduction
              - student_loan_interest)
    
    s = ('https://ja.wikipedia.org/wiki/'
         '%E3%83%97%E3%83%AD%E3%82%B0%E3%83'
         '%A9%E3%83%9F%E3%83%B3%E3%82%B0%E8%A8%80%E8%AA%9E')
    ```

## import

- `import`문은 항상 파일 맨 앞에 위치시킨다.
- 되도록이면 absolute import를 사용하고, relative import를 사용할 경우 명시적인 구문을 사용하여야 한다.
    - `import foo` (X)
    - `from . import foo` (O)
- import를 할 때는 다음의 순서로 섹션을 나누어 작성하고, 각 섹션에서는 알파벳 순서로 작성한다.
    1. Standard library 모듈
    2. Related third party 모듈
    3. Local application/library specific 모듈
