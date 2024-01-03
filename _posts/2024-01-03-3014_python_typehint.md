---
date: 2024-01-03 21:23:00 +/-0900
title: "[Python] Python Typehint 살펴보기 + 정적 언어처럼 동작 시켜보기"
categories: [Develop, python]
tags: [파이썬(python), 타입(type), 동적언어(dynamic_programming_language), 정적언어(static_programming_language), 데코레이터(decorator), inspect, typing, typehint]

---
## 개요

안녕하세요.

이번 글에서는 파이썬의 Typehint에 대해 살펴보겠습니다.

또한 typehint를 이용해 파이썬에서의 어설픈 타입체크를 수행해보겠습니다.

---
## Typehint란??

### 파이썬은 자유롭다!!! 근데 너무 자유롭다...
파이썬은 동적인 언어입니다.

예를 들어, 다음과 같은 코드는 아무 무리없이 동작합니다.

```python
a = 1
b = a * 3
print("a:", a)  # 1
print("b:", b)  # 3

a = "1"
b = a * 3
print("a:", a)  # 1
print("b:", b)  # 111
```

위와 같은 코드는 보이지 않는 곳에서 어떤 오류를 발생시킬지 모릅니다.

### 무슨 타입인지 알려준다면 어떨까?
파이썬의 `typing`모듈은 어떤 타입인지 알려주기 위해 Typehint를 제공합니다.

다음은 int로 이루어진 리스트 `b`에 변수 `a`를 삽입하는 예시입니다.

```python
from typing import List

a = 1
b:List[int] = []

b.append(a)
print(a)        # 1
print(b)        # [1]
```

### 본격적인 예시
그렇다면 이 Typehint는 어디에 사용할까요??

이 블로그 글을 조금 살펴보시면 코드 여기저기에서 사용됨을 확인할 수 있습니다.

- [[Python] Python convert PDF to Image]({% post_url 2023-11-23-3013_python_pdf_to_image %}) 중
    ```python
    from logging import Logger

    ...

    def create_logger(
        log_file_path:str = ".",
        logging_format_str:str = "[%(asctime)s] - [%(levelname)s] - %(message)s",
    ) -> Logger:
        ...
    ```

코드를 작성하면서 클래스나 메소드의 변수 간 혼동을 막기 위해 사용하고 있습니다.

## 정적 언어 흉내내기 by Typehint

### Typehint의 한계: 알려준다. 강제하지 않는다.
파이썬은 동적 언어입니다.

Typehint는 어떤 타입인지 알려줍니다.

하지만 이를 강제하지 않습니다.

어떤 타입을 강제하는 것은 파이썬의 동적 타입 언어라는 특성을 완전히 배척하는 일입니다.

그렇다면 이를 활용해서 정적 언어를 흉내낼 수는 없을까요??

### 돌아가기 위한 간단한 준비물

#### inspect 모듈

`inspect` 모듈은 모듈, 클래스, 함수 등 라이브 객체의 정보를 접근할 때 사용합니다.

타입 확인하기, 소스코드 가져오기, 클래스와 함수 검사, 인터프리터 스택 측정 등 4가지의 주요 서비스가 존재합니다.

여기서는 `inspect.signature` 메소드를 통해 매개변수와 반환값에 대한 타입 힌트를 확인할 수 있습니다.

#### 데코레이터

파이썬의 데코레이터는 함수나 클래스의 원래 기능에 새로운 기능을 추가하고자 할 때 사용합니다.

간단한 예제는 다음과 같습니다.

```python
def add_emoji(func):                    # 호출할 함수를 매개변수로 받음
    def wrapper():                      # 호출할 함수를 감싸는 함수
        print(func.__name__, '😃')      # __name__으로 함수 이름 출력
        func()                          # 매개변수로 받은 함수를 호출
    return wrapper                      # wrapper 함수 반환

@add_emoji
def print_info():
    print("Hello. This is test file!!")

print_info()
```

이 코드는 `print_info()` 함수에 `add_emoji` 데코레이터를 추가한 예시입니다.

기존의 출력 텍스트인 'Hello. This is test file!!'를 출력하기 이전에 데코레이터를 먼저 실행하므로 이모지를 먼저 출력합니다.

추후 다른 글을 통해 데코레이터를 소개하겠습니다.

#### 재귀

재귀는 알고리즘 기법 중 하나입니다.

자기 자신을 호출하는 재귀 함수를 통해 구현되는 알고리즘은생각보다 많습니다.

보통 특정한 문제를 더 작은 문제로 쪼개서 해결할 수 있을 때 사용합니다.

가장 대표적인 예시는 피보나치 수열입니다.

피보나치 수열은 1, 1로 시작하며 n번째 항은 (n-1번째 항) + (n-2번쨰 항)으로 구성된 수열을 의미합니다.

이를 재귀 함수를 통해 구현한다면 다음과 같습니다.

```python
def fibo(n):
    if n < 2:
        return n
    else:
        return fibo(n-1) + fibo(n-2)
```

`fino()` 함수에서 다시 `fibo()` 함수를 호출하는 형태를 보입니다.

한편, n의 값이 2보다 작다면 자기자신을 호출하지 않고 정수를 반환합니다.

위의 코드처럼 재귀 함수에서 가장 중요한 것은

1. 자기 자신을 호출
2. 호출에서의 탈출 조건

입니다.

### 약간의 편법

그렇다면 필요한 개념을 살펴봤으니 본격적으로 편법을 써보겠습니다.

```python
from functools import wraps
from typing import Tuple, get_origin, get_args
import inspect


def type_check_decorator(_param=True, _return=True):
    """정적인 타입 체크

    Args:
        _param (bool, optional): 파라미터 체크 여부. Defaults to True.
        _return (bool, optional): 반환값 체크 여부. Defaults to True.
    """
    def wrapper_with_args(func):
        sig = inspect.signature(func)
        params = sig.parameters

        def is_equal_value_and_typehint(value, type_hint) -> bool:
            """Generic 타입을 포함한 변수와 type hint의 비교 함수

            Args:
                value (_type_): 타입을 확인할 변수
                type_hint (_type_): type_hint 값

            Returns:
                bool: 비교 결과
            """
            origin = get_origin(type_hint)
            args = get_args(type_hint)
            
            if type_hint == Any:
                return True
            elif origin is None:  # Not a generic type
                return isinstance(value, type_hint)
            elif isinstance(origin, types.UnionType) or isinstance(type_hint, types.UnionType):
                return isinstance(value, type_hint)
            elif len(args) > 1:
                if (isinstance(value, Mapping) and all(isinstance(each_key, args[0]) for each_key in value.keys()) and all(isinstance(each_value, args[1]) for each_value in value.values())) \
                    or (not isinstance(value, Mapping) and all(is_equal_value_and_typehint(v, a) for v, a in zip(value, args))):
                    return True
                else:
                    return False
            elif isinstance(value, origin):
                args = get_args(type_hint)
                if len(args) == 0:  # Generic with no args, e.g. List
                    return True
                elif all(is_equal_value_and_typehint(v, args[0]) for v in value):
                    return True
            return False
        
        @wraps(func)
        def wrapper_inner(*args, **kwargs):
            """변수+반환값과 type hint를 비교

            Raises:
                TypeError: 타입이 맞지 않는 경우
            """
            if _param:
                bound_values = sig.bind(*args, **kwargs)
                for name, value in bound_values.arguments.items():
                    if params[name].annotation is not inspect._empty and \
                    not is_equal_value_and_typehint(value, params[name].annotation):
                        raise TypeError(f"Argument {name}={value} is not of expected type {params[name].annotation}")
            
            result = func(*args, **kwargs)
            if _return and \
                sig.return_annotation is not inspect._empty and \
                not is_equal_value_and_typehint(result, sig.return_annotation):
                raise TypeError(f"Return value {result} is not of expected type {sig.return_annotation}")
            return result

        return wrapper_inner
    return wrapper_with_args

# 데코레이터의 옵션을 다르게 설정하며 테스트 진행
for each_deco_config in [{}, {"_param":False}, {"_return":False}, {"_param":False, "_return":False}]:
    try:
        @type_check_decorator(**each_deco_config)
        def test_func(var1:int, var2:str) -> Tuple[int, int]:
            print("var1:", var1 + 10)
            print("var2:", var2)
            
            return 3

        print(f"deco_config::{each_deco_config} - Start!!!")
        
        test_func(1, "2")
        test_func(1, 2)
    except Exception as e:
        print(f"Error...  - {e}")
    else:
        print(f"End!!!")
    finally:
        print("====================")
```

`type_check_decorator` 데코레이터는 정적으로 타입힌트를 확인합니다.

우선 inspect 모듈을 이용해 인자와 반환값의 타입힌트를 가져옵니다.

이후 각각의 값과 타입힌트를 `is_equal_value_and_typehint()`를 통해 확인합니다.

만약 해당하는 값의 요소들이 존재한다면 재귀함수를 통해 동일한 작업을 진행합니다.

리스트 등 각 요소들의 값을 모두 확인해 일치하지 않는다면 `TypeError`를, 그렇지 않다면 정상으로 통과합니다.

이렇게 typehint와 위에서 언급한 약간의 개념으로 정적 언어를 흉내내는 데 성공했습니다!!

물론, 위 코드는 아직 테스트 중이어서 **<u>완벽한 타입체크는 되지 않는다</u>**는 점을 참고해주세요!!

---
## 마무리하며

이번 글에서는 파이썬의 Typehint와 이를 활용한 정적 언어 흉내내기를 진행했습니다.

"큰 힘에는 큰 책임이 따른다" 라는 스파이더맨의 명대사가 있죠.

파이썬의 자유로운 타입에는 예기치 못한 오류라는 그림자가 존재합니다.

typehint이 작은 촛불처럼 이 그림자를 비추지만 혼자서는 아직 무리가 있어보이네요...!!

위에서 사용한 편법은 부족한 부분이 매우매우 많습니다.

예를 들어, 정적 언어에 비해 느린 동적 언어 파이썬에 재귀함수로 작성된 데코레이션까지 추가해 더더욱 느려졌습니다.

그러므로 "파이썬의 typehint를 이렇게도 쓰는구나..?"라고 생각해주시면 감사하겠습니다.

이 글이 조금이나마 도움이 되었으면 합니다.

감사합니다. 😀

---
## 참고 문헌

- typing — 형 힌트 지원, [https://docs.python.org/ko/3/library/typing.html?highlight=typing#](https://docs.python.org/ko/3/library/typing.html?highlight=typing#)
- inspect — 라이브 객체 검사, [https://docs.python.org/ko/3/library/inspect.html#introspecting-callables-with-the-signature-object](https://docs.python.org/ko/3/library/inspect.- html#introspecting-callables-with-the-signature-object)
- Python Generic Type, [https://hides.kr/1106](https://hides.kr/1106)
- [알고리즘] 깊이 우선 탐색(DFS) 알고리즘에 대해 알아보자!(+Python 구현), [https://heytech.tistory.com/55](https://heytech.tistory.com/55)
- 데코레이터 [https://dojang.io/mod/page/view.php?id=2429](https://dojang.io/mod/page/view.php?id=2429)
