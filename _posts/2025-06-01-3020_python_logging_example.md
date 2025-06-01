---
date: 2025-06-01 17:06:00 +/-0900
title: "[Python] 파이썬 예외(Exception) - Enum과 데코레이터 활용하기"
categories: [Develop, python]
tags: [파이썬(python), 예외(exception), 데코레이터(decorator), enum]
description: 프로젝트의 예외 로직을 리팩토링하면서 수행한 내용을 공유합니다.

---
## 개요
안녕하세요.

이번 글은 프로젝트의 예외 처리 부분을 리팩토링하면서 Enum과 데코레이터를 어떻게 활용했는지 공유하겠습니다.

---
## 예외 처리: 아는 문제와 모르는 문제에 대해

### 개인적인 예외의 정의

오류와 예외는 얼핏 보면 유사한 개념인데요. 자바에서는 오류를 크게 "프로그램 코드에 의해 수습될 수 없는 심각한 오류"인 에러와 "프로그램 코드에 의해 수습될 수 있는 미약한 오류" 예외로 구분합니다. 각각의 정의는 사람마다 다르겠지만 저는 "시스템에서 발생하는 모든 문제"를 오류로 "발생할 수 있는 오류를 정의한 것"을 예외라고 생각합니다. 예를 들어, "데이터베이스에 접근하는 계정의 인증 정보가 올바르지 않다"라는 내용은 오류에 해당하고 이를 데이터베이스 인증 오류라는 예외로 정의하는 방식이죠.

### 예외 처리가 필요한 이유

그렇다면 예외를 정의하고 또 이를 처리하는 방법을 구현하는 이유가 있을까요? 어떤 시스템이라도 오류가 발생합니다. 오류가 발생한다면 시스템이 올바르게 동작하기 어렵습니다. 따라서 발생할 수 있는 오류를 모으고 이를 따로 처리해서 시스템에 견고함과 유연함을 부여할 수 있습니다. 이 과정이 바로 예외 처리입니다.

### 예외와 예외 처리: 아는 문제와 풀이법

그래서 저는 예외와 예외 처리를 각각 아는 문제와 풀이법에 비유합니다. 문제를 알고 있기 때문에 어떤 풀이법을 사용할지 정하고 정답을 맞출 수 있는거죠! 그렇기에 오류라는 거대한 문제의 집단에서 최대한 예외라는 아는 문제를 찾아내고 예외 처리라는 올바른 풀이를 적용해야 합니다.

하지만 그 과정이 생각만큼 간단하지는 않습니다. 위의 데이터베이스 인증 오류와 같이 비교적 간단한 예외도 있지만 시스템의 로직이 실제 환경에서 실행되어야만 발견할 수 있는 오류도 분명히 존재합니다. 예외로 정의하지 않아 모르는 문제에 해당하는 오류라고 할 수 있어요. 그러므로 가능한 예외를 정의하고 예외 처리를 구현하되 예기치 않은 오류가 발생해도 전체 시스템에는 문제가 없는 로깅이나 알림 같은 최소한의 로직도 필요합니다.

---
## 예외 로직 리팩토링 과정

### 파이썬의 예외: Exception 클래스

예외와 예외 처리에 대해 알아봤으니 파이썬 코드를 통해 실제 구현을 확인해보겠습니다. 그에 앞서 파이썬의 예외 처리 방법인 `try ... except` 문과 `Exception` 클래스를 아래의 예시 코드와 함께 간단히 짚어보고 가겠습니다.

```python
# 숫자 나누기에서 발생할 수 있는 예외 처리
try:
    a = int(input("숫자 a를 입력하세요: "))
    b = int(input("숫자 b를 입력하세요: "))
    result = a / b
except ValueError:
    print("숫자를 입력해야 합니다.")
except ZeroDivisionError:
    print("0으로 나눌 수 없습니다.")
else:
    print("결과:", result)
finally:
    print("프로그램 종료")
```

예시는 사용자가 입력한 숫자 2개를 나누는 코드입니다. `try` 문에서는 예외가 발생할 수 있는 구문이 위치합니다. 뒤이어 나오는 `except` 문은 각 예외가 발생했을 때 처리하는 구문을 작성합니다. 여기서는 숫자가 아닌 예외와 0으로 나누는 예외를 처리하는군요.

`else`와 `finally` 문은 선택적으로 활용합니다. `else` 문은 예외가 발생하지 않았을 때 실행하며 `finally` 문은 예외 발생 여부와 관계없이 실행합니다. 예시의 경우 예외가 발생하지 않았을 때만 정상적으로 나눈 결과를 보여주고 마지막으로 프로그램 종료를 알리네요.

### 리팩토링 내용 (1): enum을 이용해 예외를 변수화하자!

파이썬으로 구성된 내부 서비스에서 저는 예외 로직을 리팩토링했습니다. 구체적으로는 예외의 정보를 `Enum`을 통해 변수로 변경하고 `decorator`로 이를 주입했으며 공통의 예외 처리기를 정의했습니다.

다음은 실제 코드들입니다. 가장 먼저 예외 정보를 저장하는 코드입니다. Enum을 활용하여 오류 코드와 오류 정보를 저장합니다.

```python
from enum import Enum


class ExceptionType(Enum):
    """예외 타입
    """
    BASE_EXCEPTION = (999, "정의되지 않은 오류")

    DATA_SOURCE_EXCEPTION = (100, "데이터 소스 오류")
    DATA_SOURCE_MYSQL_EXCEPTION = (110, "MySQL 오류")
    DATA_SOURCE_MYSQL_CONFIGURE_EXCEPTION = (111, "MySQL 설정 오류")
    DATA_SOURCE_MYSQL_SESSION_EXCEPTION = (112, "MySQL 세션 오류")
    ...
```

### 리팩토링 내용 (2): decorator를 활용해 예외를 매핑하자!

다음 코드는 예외의 최상위 클래스입니다. 아래의 함수는 각 예외 클래스에 사용하는 데코레이터로 직전의 `ExceptionType`과 예외 클래스를 실제로 매핑하는 역할입니다.

```python
from exception_type import ExceptionType


class BaseException(Exception):
    @classmethod
    def set_exception_code(cls, exception_code: int):
        cls.exception_code = exception_code

    @classmethod
    def set_exception_message(cls, exception_message: str):
        cls.exception_message = exception_message

    @property
    def custom_message(self):
        return self.__custom_message

    @custom_message.setter
    def custom_message(self, custom_message: str):
        self.__custom_message = custom_message

    def __init__(self, message=None):
        self.exception_code = self.__class__.exception_code
        self.exception_message = self.__class__.exception_message
        self.custom_message = message
        super().__init__(message)

    def __str__(self) -> str:
        messages = [f"[{self.exception_code:08}]", self.exception_message]
        if self.custom_message:
            messages.append(self.custom_message)
        return " - ".join(messages)


def with_exception_type(exception_type: ExceptionType):
    def set_code_and_message(cls: BaseException):
        cls.set_exception_code(exception_type.value[0])
        cls.set_exception_message(exception_type.value[1])
        return cls

    return set_code_and_message
```

각 예외의 클래스에는 동일한 오류 코드와 오류 정보를 매핑하고 구별이 필요한 메시지를 따로 저장합니다. 실제 클래스에 정의한 내용은 따로 없습니다. 단지 이전에 정의한 데코레이터를 통해 오류 코드와 메시지를 매핑하고, 문서화를 위한 docstring이 있을 뿐이죠. 리팩토링한 서비스는 계층적인 오류 구조를 위해 상속 관계의 클래스가 다수 존재했습니다.

```python
from base_exception import BaseException, with_exception_type
from exception_type import ExceptionType


@with_exception_type(ExceptionType.DATA_SOURCE_EXCEPTION)
class DataSourceException(BaseException):
    """데이터 소스 오류 중 정의되지 않은 오류"""


@with_exception_type(ExceptionType.DATA_SOURCE_MYSQL_EXCEPTION)
class MysqlException(DataSourceException):
    """MySQL 오류 중 정의되지 않은 오류"""


@with_exception_type(ExceptionType.DATA_SOURCE_MYSQL_CONFIGURE_EXCEPTION)
class MysqlConfigureException(MysqlException):
    """MySQL 설정 오류"""


@with_exception_type(ExceptionType.DATA_SOURCE_MYSQL_SESSION_EXCEPTION)
class MysqlSessionException(MysqlException):
    """MySQL 세션 오류"""
...
```

### 리팩토링 내용 (3): 예외를 공통으로 처리하자!

마지막으로 볼 예외 처리 코드는 공통의 예외 처리기입니다.

```python
import logging

from base_exception import BaseException
from exception_type import ExceptionType


def handle_exception(exc: Exception, logger: logging.logger):
    """공통 예외 처리

    - 커스텀 예외(BaseException 계열)는 코드/메시지/추가 메시지 출력
    - 그 외 예외는 정의되지 않은 오류로 처리
    """
    processed_exc = exc
    if not isinstance(exc, BaseException):
        exception_code, exception_message = ExceptionType.BASE_EXCEPTION.value
        BaseException.set_exception_code(exception_code)
        BaseException.set_exception_message(exception_message)
        processed_exc = BaseException(exc.args[0])

    logger.error(processed_exc, exc_info=True)
    return processed_exc


def handle_exception_decorator(exception: BaseException, custom_message: str = ""):
    def decorator_handle_exception(func):
        def wrapper(self, *args, **kwargs):
            try:
                return func(self, *args, **kwargs)
            except Exception as e:
                processed_exc = handle_exception(e, self.logger)
                raise processed_exc

        return wrapper

    return decorator_handle_exception
```

비슷한 이름의 함수 2개가 존재합니다. `handle_exception`은 실제 예외를 처리하는 부분으로 최상위 예외 클래스에 해당하지 않는 오류를 최상위 예외 클래스로 감싸고 logger의 error 메시지를 남깁니다. 예외 처리가 필요한 함수에는 `handle_exception_decorator`를 사용하는데요. 데코레이터가 위치한 함수를 `try except`로 감싸고 예외에 대한 처리는 `handle_exception`에게 넘기는 역할입니다.

### 리팩토링 결과

실제 로직에서는 다음과 같이 작성합니다.

```python
class MySQLClient:
    ...

    @handle_exception_decorator(MysqlSessionException, "세션 커밋 오류")
    def commit_session(self) -> None:
        self.session.commit()
...
```

위 예시에서 `handle_exception_decorator`에 전달한 "세션 커밋 오류"는 예외 발생 시 custom_message로 전달됩니다. 예를 들어, `commit_session`에서 예외가 발생하면 로그에는 다음과 같이 custom_message가 포함된 메시지가 출력됩니다.

```python
try:
    client.commit_session()
except MysqlSessionException as e:
    print(e)  # [00000112] - MySQL 세션 오류 - 세션 커밋 오류
```

이처럼 custom_message를 활용하면 예외 상황에 대한 추가적인 맥락을 로그나 메시지에 명확하게 남길 수 있습니다. 이로써 예외와 관련된 리팩토링을 마무리하겠습니다.

---
## 마무리하며

이번 글에서는 오류와 예외에 대한 제 개인적인 정의와 실제 리팩토링 코드를 공유했습니다. 이 구조는 예외 타입과 메시지를 Enum과 데코레이터로 일원화하여 관리하기 때문에 여러 모듈이나 패키지에서 공통 예외 처리 및 로깅/알림 시스템과 쉽게 연계할 수 있습니다. 예외 계층 구조를 확장하거나, 새로운 예외 타입을 추가할 때도 Enum과 데코레이터만 정의하면 되므로 유지보수하기에도 용이하더라구요! 물론 더 나은 구조들도 많겠지만요!

최근에 데이터 정합성을 확인하는 서비스를 구현하면서 예외를 어떻게 정의할지 고민이 많아졌습니다. 이번 글과 같이 리팩토링만 한다면 목표가 명확하지만 처음부터 쌓아가니 전혀 다른 이야기더라구요. 고려할 부분도 놓친 부분도 많아 바쁜 하루를 보내고 있습니다. 비록 약간의 코드 조각이지만, 저와 같이 낯선 길을 걸어가는 분들에게 도움이 되기를 바랍니다.

감사합니다. 😀

---
## 참고 문헌

- "로깅 HOWTO", Python 3.13.3 문서, [https://docs.python.org/ko/3.13/howto/logging.html](https://docs.python.org/ko/3.13/howto/logging.html)
- "42.1 데코레이터 만들기", 파이썬 코딩 도장, [https://dojang.io/mod/page/view.php?id=2427](https://dojang.io/mod/page/view.php?id=2427)
