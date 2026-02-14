# Python Coding Convention

> **PEP 8 (Python Enhancement Proposal 8)** 기반
>
> - 공식 문서: https://peps.python.org/pep-0008/
> - 관리: Python Software Foundation (Guido van Rossum, Barry Warsaw, Alyssa Coghlan)
> - 관련 도구: Flake8, Pylint, pycodestyle, Ruff, Black, autopep8

---

## 1. 선정 사유

| 기준 | PEP 8 | Google Python | Black | Ruff |
|---|---|---|---|---|
| 공인 표준 | 공식 Python 스타일 가이드 | PEP 8 확장 | PEP 8 준수 포매터 | 린터/포매터 |
| 포괄성 | 네이밍, 포매팅, import, 주석, 프로그래밍 관행 전체 | 높음 (기업 특화 규칙 추가) | 포매팅만 | 린트 규칙만 |
| 커뮤니티 채택 | 보편적 | 기업 환경에서 높음 | 매우 높음 (django, pytest 등) | 급성장 |
| 도구 생태계 | 모든 주요 도구가 PEP 8 기반 | Pylint 기반 | 자체 포매터 | 자체 린터+포매터 |

PEP 8은 Python의 공식 스타일 가이드이며, 다른 모든 가이드(Google, Black, Ruff)의 기반이다. Python 코드를 작성하는 데 있어 보편적으로 적용되는 유일한 표준이다.

---

## 2. 코드 레이아웃

### 2.1 들여쓰기

- **4칸 공백** 사용 (탭 금지)
- Python 3은 탭과 공백 혼용을 금지
- 연속 줄 정렬 방식:

```python
# (1) 여는 구분자에 수직 정렬
foo = long_function_name(var_one, var_two,
                         var_three, var_four)

# (2) 행잉 들여쓰기 (첫 줄에 인자 없음, 추가 들여쓰기로 본문과 구분)
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)
```

- 닫는 괄호/대괄호/소괄호 위치:
  - 마지막 항목의 첫 비공백 문자 아래
  - 구문을 시작하는 줄의 첫 문자 아래

### 2.2 최대 줄 길이

| 대상 | 제한 |
|---|---|
| 코드 | **79자** |
| 독스트링/주석 | **72자** |
| 팀 합의 시 코드 | 최대 99자 |

- 선호하는 줄 바꿈: 괄호, 대괄호, 중괄호 안의 암시적 연속
- 백슬래시: `with` 문과 `assert` 문에서 암시적 연속 불가 시 허용

### 2.3 이항 연산자 주변 줄 바꿈

```python
# 권장: 이항 연산자 앞에서 줄 바꿈 (Knuth 스타일)
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends))
```

### 2.4 빈 줄

| 위치 | 빈 줄 수 |
|---|---|
| 최상위 함수/클래스 정의 전후 | **2줄** |
| 클래스 내 메서드 정의 전후 | **1줄** |
| 함수 내 논리적 섹션 | 필요 시 (절제하여) |

### 2.5 소스 파일 인코딩

- UTF-8 (Python 3 기본값), 인코딩 선언 불필요
- Non-ASCII 문자 절제하여 사용
- 표준 라이브러리 식별자는 ASCII 전용, 영어 단어 사용

---

## 3. Import

### 3.1 Import 포매팅

```python
# 올바름:
import os
import sys

# 잘못됨:
import sys, os

# 허용:
from subprocess import Popen, PIPE
```

### 3.2 Import 배치

파일 최상단, 모듈 주석과 독스트링 뒤, 모듈 전역/상수 앞.

### 3.3 Import 그룹 (순서대로, 각 그룹 사이 빈 줄)

1. **표준 라이브러리** import
2. **관련 서드파티** import
3. **로컬 애플리케이션/라이브러리** import

### 3.4 Import 스타일

```python
# 절대 import (권장)
import mypkg.sibling
from mypkg import sibling
from mypkg.sibling import example

# 명시적 상대 import (허용)
from . import sibling
from .sibling import example
```

- **와일드카드 import 금지**: `from module import *` 회피 (네임스페이스 불명확)

### 3.5 모듈 수준 던더 이름

`__all__`, `__author__`, `__version__` 등은 모듈 독스트링 뒤, import 앞에 배치. `from __future__` import는 독스트링을 제외한 모든 코드보다 앞에 위치.

---

## 4. 문자열 따옴표

- 작은따옴표와 큰따옴표는 동등. 하나를 선택하고 일관성 유지
- 문자열에 따옴표 포함 시 다른 스타일 사용 (백슬래시 회피)
- **삼중 따옴표 문자열**: 항상 큰따옴표(`"""`) 사용 (PEP 257 일관성)

---

## 5. 공백

### 5.1 불필요한 공백 회피

```python
# 올바름:
spam(ham[1], {eggs: 2})
foo = (0,)
if x == 4: print(x, y); x, y = y, x
spam(1)
dct['key'] = lst[index]

# 잘못됨:
spam( ham[ 1 ], { eggs: 2 } )
foo = (0, )
if x == 4 : print(x , y) ; x , y = y , x
spam (1)
dct ['key'] = lst [index]
```

### 5.2 정렬을 위한 여분의 공백 금지

```python
# 올바름:
x = 1
y = 2
long_variable = 3

# 잘못됨:
x             = 1
y             = 2
long_variable = 3
```

### 5.3 슬라이스 공백

```python
# 올바름:
ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3]
ham[lower::2], ham[:upper]
ham[lower+offset : upper+offset]

# 잘못됨:
ham[lower + offset:upper + offset]
ham[1: 9], ham[1 :9]
```

### 5.4 이항 연산자 공백

- 양쪽에 **한 칸 공백**: 할당(`=`, `+=`), 비교(`==`, `!=`, `<`), 불린(`and`, `or`, `not`)
- 혼합 우선순위: 가장 **낮은** 우선순위 주변에만 공백:
  ```python
  hypot2 = x*x + y*y
  c = (a+b) * (a-b)
  ```

### 5.5 함수 어노테이션

```python
def munge(input: AnyStr): ...
def munge() -> PosInt: ...
```

### 5.6 키워드 인자와 기본값

```python
# 어노테이션 없는 경우: = 주변 공백 없음
def complex(real, imag=0.0):
    return magic(r=real, i=imag)

# 어노테이션과 기본값 결합 시: = 주변 공백
def munge(sep: AnyStr = None): ...
```

### 5.7 복합문

- 일반적으로 비권장 (한 줄에 여러 문장)
- 다중 절 `if`/`for`/`while`의 본문을 같은 줄에 놓지 않음

### 5.8 후행 공백

모든 곳에서 후행 공백 회피.

---

## 6. 후행 쉼표

- 단일 요소 튜플에서 **필수**: `FILES = ('setup.cfg',)`
- 확장 예정 목록에서 각 값을 자체 줄에 배치 + 후행 쉼표:

```python
FILES = [
    'setup.cfg',
    'tox.ini',
]
initialize(
    FILES,
    error=True,
)
```

---

## 7. 주석

### 7.1 일반 규칙

- 코드와 모순되는 주석은 주석이 없는 것보다 **나쁘다**
- 주석은 **완전한 문장**으로 작성
- 첫 단어 대문자 (소문자 식별자인 경우 제외)
- 영어로 작성 (해당 언어 사용자만 읽는 것이 120% 확실하지 않은 한)

### 7.2 블록 주석

- 뒤따르는 코드에 적용
- 코드와 같은 수준으로 들여쓰기
- 각 줄 `#` + 공백 한 칸으로 시작
- 문단 구분: `#`만 있는 줄

### 7.3 인라인 주석

- **절제하여** 사용
- 문장에서 **최소 2칸 공백** 뒤에 위치
- `#` + 공백으로 시작
- 자명한 내용 진술 금지

### 7.4 독스트링 (PEP 257)

- 모든 **공개** 모듈, 함수, 클래스, 메서드에 작성
- `"""삼중 큰따옴표"""` 사용
- **한 줄짜리**: 닫는 `"""`을 같은 줄에:
  ```python
  """Return the pathname of the KOS root directory."""
  ```
- **여러 줄짜리**: 닫는 `"""`을 자체 줄에:
  ```python
  """Summary line.

  Extended description of function.

  """
  ```
- 요약줄: 마침표로 끝나는 구. 명령형으로 작성 ("Return X", "Do X" - "Returns X" 아님)
- 클래스 독스트링 뒤에 빈 줄 (첫 번째 메서드 앞)

---

## 8. 네이밍 규칙

### 8.1 빠른 참조 표

| 유형 | 규칙 | 예시 |
|---|---|---|
| 패키지 | 짧은 전소문자, 언더스코어 비선호 | `mypackage` |
| 모듈 | 짧은 전소문자, 언더스코어 허용 | `my_module` |
| 클래스 | CapWords (CamelCase) | `MyClass`, `HTTPServer` |
| 예외 | CapWords + "Error" 접미사 | `ValueError`, `ConnectionError` |
| 함수 | lowercase_with_underscores | `calculate_total()` |
| 메서드 | lowercase_with_underscores | `get_name()` |
| 인스턴스 변수 | lowercase_with_underscores | `first_name` |
| 상수 | UPPER_CASE_WITH_UNDERSCORES | `MAX_OVERFLOW`, `PI` |
| 타입 변수 | CapWords, 짧게 | `T`, `AnyStr`, `Num` |
| 전역 변수 | 함수와 동일 | `current_user` |

### 8.2 특수 접두/접미사 규칙

| 패턴 | 의미 | 예시 |
|---|---|---|
| `_single_leading_underscore` | "내부 사용" 표시 | `_internal_helper` |
| `single_trailing_underscore_` | Python 키워드와의 충돌 회피 | `class_`, `type_` |
| `__double_leading_underscore` | 이름 맹글링 (클래스 프라이빗) | `__private_attr` |
| `__dunder__` | 매직/특수 메서드 (자체 생성 금지) | `__init__`, `__str__` |

### 8.3 피해야 할 이름

단일 문자 변수로 `l`(소문자 L), `O`(대문자 O), `I`(대문자 I) 절대 사용 금지 - 많은 글꼴에서 `1`, `0`과 혼동.

### 8.4 메서드 인자

- 인스턴스 메서드 첫 인자: 항상 `self`
- 클래스 메서드 첫 인자: 항상 `cls`
- 예약어와 충돌 시: 후행 언더스코어 추가 (`class_`)

### 8.5 약어

- CapWords에서 약어의 모든 글자 대문자화: `HTTPServerError` (O), `HttpServerError` (X)

### 8.6 공개 vs 비공개 인터페이스

- 공개 속성: 선행 언더스코어 없음
- `__all__`로 모듈의 공개 API 명시적 선언
- 내부 인터페이스: 선행 언더스코어 사용
- 의심스러우면 **비공개**로 만듦 (나중에 공개하기가 더 쉬움)

### 8.7 상속 설계

- 서브클래스에 사용시키고 싶지 않은 속성: 이중 선행 언더스코어 (이름 맹글링) 고려
- 단순 공개 데이터 속성: getter/setter 메서드 작성 금지, 직접 노출
- 나중에 기능적 동작 필요 시 `@property` 사용
- `@property`는 부작용 없어야 함 (캐싱은 허용)

---

## 9. 프로그래밍 권장 사항

### 9.1 싱글톤 비교

```python
# 올바름:
if foo is not None:
if foo is None:

# 잘못됨:
if foo != None:
if foo == None:
```

`is` / `is not` 사용, `==` / `!=` 금지.

### 9.2 Boolean 비교

```python
# 올바름:
if greeting:

# 잘못됨:
if greeting == True:

# 더 나쁨:
if greeting is True:
```

### 9.3 시퀀스 참/거짓

```python
# 올바름 (빈 시퀀스는 거짓):
if not seq:
if seq:

# 잘못됨:
if len(seq):
if not len(seq):
```

### 9.4 타입 체크

```python
# 올바름:
if isinstance(obj, int):

# 잘못됨:
if type(obj) is type(1):
```

### 9.5 문자열 접두/접미사 체크

```python
# 올바름:
if foo.startswith('bar'):
if foo.endswith('baz'):

# 잘못됨:
if foo[:3] == 'bar':
if foo[-3:] == 'baz':
```

### 9.6 함수 정의 vs Lambda 할당

```python
# 올바름:
def f(x): return 2*x

# 잘못됨:
f = lambda x: 2*x
```

Lambda를 이름에 할당하면 lambda의 유일한 이점(표현식 임베딩)이 사라짐.

### 9.7 예외 처리

- `Exception`에서 파생 (`BaseException` 아님)
- 예외 클래스 이름에 `Error` 접미사
- `except` 절에 구체적 예외 타입 사용 - 맨 `except:` 금지

```python
# 올바름:
try:
    ...
except ValueError:
    ...

# 잘못됨:
try:
    ...
except:
    ...
```

- 모든 프로그램 오류 캐치 필요 시: `except Exception:` 사용
- `try` 절 본문을 **최소한**의 코드로 유지
- 예외 체이닝: `raise X from Y`
- 의도적 예외 교체: `raise X from None`

### 9.8 Return 문 일관성

```python
# 올바름: 모든 return이 표현식을 반환하거나, 모두 반환하지 않음
def foo(x):
    if x >= 0:
        return math.sqrt(x)
    else:
        return None

# 잘못됨:
def foo(x):
    if x >= 0:
        return math.sqrt(x)
```

### 9.9 문자열 연결

- CPython의 `a += b` 문자열 최적화에 의존 금지
- 성능 민감한 문자열 빌딩: `''.join()` 사용 (선형 시간 보장)

### 9.10 리소스 관리

```python
# 올바름:
with open('file.txt') as f:
    data = f.read()
```

리소스 획득/해제에 `with` 문 사용.

### 9.11 순서 연산

순서 연산 구현 시 6개 전부 구현: `__eq__`, `__ne__`, `__lt__`, `__le__`, `__gt__`, `__ge__`. 또는 `@functools.total_ordering` 사용.

### 9.12 `finally` 내 흐름 제어

`finally` 블록 내에서 `return`, `break`, `continue` 사용 금지 - 활성 예외를 조용히 취소함.

---

## 10. 타입 어노테이션 (PEP 484 / PEP 526)

### 10.1 함수 어노테이션

```python
def greeting(name: str) -> str:
    return 'Hello ' + name
```

### 10.2 변수 어노테이션

```python
# 올바름:
code: int
label: str = '<unknown>'

# 잘못됨:
code:int
code : int
label: str='<unknown>'
```

### 10.3 모던 패턴 (Python 3.9+/3.10+)

- 내장 제네릭 선호: `list[str]` > `List[str]`, `dict[str, int]` > `Dict[str, int]`
- `X | None` (3.10+) > `Optional[X]`

### 10.4 타입 체커

- 타입 체커(mypy, pytype, pyright)는 **선택적 별도 도구**
- Python 인터프리터는 런타임에 어노테이션을 검사/강제하지 않음
- 필요 시 `# type: ignore`로 타입 체커 경고 억제

---

## 11. 독스트링 규칙 (PEP 257)

### 11.1 한 줄 독스트링

```python
def kos_root():
    """Return the pathname of the KOS root directory."""
    global _kos_root
    ...
```

- 닫는 `"""` 같은 줄, 전후 빈 줄 없음, 마침표로 끝나는 구, 명령형

### 11.2 여러 줄 독스트링

```python
def complex_function(real=0.0, imag=0.0):
    """Form a complex number.

    Keyword arguments:
    real -- the real part (default 0.0)
    imag -- the imaginary part (default 0.0)

    """
    ...
```

- 첫 줄에 요약, 빈 줄, 상세 설명
- 닫는 `"""` 자체 줄에
- 클래스 독스트링 뒤 빈 줄 (첫 메서드 앞)

### 11.3 모듈/클래스/스크립트 독스트링

- **모듈**: export되는 클래스, 예외, 함수 및 기타 객체를 한 줄 요약과 함께 나열
- **클래스**: 동작 요약, 공개 메서드/인스턴스 변수 나열, `__init__`에 자체 독스트링
- **스크립트**: "사용법" 메시지 역할, 함수/명령줄 구문/환경 변수/파일 문서화

---

## 12. 핵심 수치 요약

| 규칙 | 값 |
|---|---|
| 들여쓰기 | 4칸 공백 |
| 최대 줄 길이 (코드) | 79자 |
| 최대 줄 길이 (주석/독스트링) | 72자 |
| 선택적 팀 줄 길이 | 최대 99자 |
| 최상위 정의 전후 빈 줄 | 2줄 |
| 클래스 내 메서드 전후 빈 줄 | 1줄 |
| 이항 연산자 주변 공백 | 1칸 |
| 함수 호출 괄호 앞 공백 | 0 |
| Import 그룹 구분 | 빈 줄 1줄 |

---

## 참고 자료

- [PEP 8 -- Style Guide for Python Code (Official)](https://peps.python.org/pep-0008/)
- [PEP 257 -- Docstring Conventions](https://peps.python.org/pep-0257/)
- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)
- [Black -- The Uncompromising Code Formatter](https://github.com/psf/black)
- [Ruff -- Python Linter and Formatter](https://github.com/astral-sh/ruff)
- [How to Write Beautiful Python Code With PEP 8 -- Real Python](https://realpython.com/python-pep8/)
