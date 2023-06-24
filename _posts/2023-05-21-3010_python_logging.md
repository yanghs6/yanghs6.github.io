---
date: 2023-05-21 16:26:00 +/-0900
title: "[Python] Python Logging 기본"
categories: [Develop, python]
tags: [파이썬(python), 로깅(logging)]

---
## 개요

안녕하세요.

이번 글에서는 파이썬의 logging에 대해 살펴보겠습니다.

---
## Logging

로깅은 로그를 기록하는 행위를 뜻합니다.

### Log가 정확히 뭔가요???

로그란 운영 체제나 다르소프트웨어가 실행 중에 발생하는 이벤트나 각기 다른 사용자의 통신 소프트웨어 간의 메시지를 기록한 파일입니다.

주로 활용하고 싶은 정보를 모으고자 작성합니다.

### 그렇다면 어떤 내용을 기록하나요??

로그는 데이터베이스의 트랜잭션 정보, 로그인을 위한 API 호출 정보, 특정 사용자의 웹피이지 접근 등 다양한 소스에서 각종 내용을 기록됩니다.

방금 이야기한 로그들은 다음과 같은 곳에서 활용 가능합니다.

![로그 활용 예시](/assets/img/develop/3010/3010_01_log_usage_example.png)
_[그림 1] 로그 활용 예시_

---
## 기초: 로그 남겨보기

### 예시 코드

```python
import logging


logging.basicConfig(filename="01_first_logging.log", encoding="utf-8", level="DEBUG")

logging.debug("log_level::DEBUG  -  message::Detailed information, typically of interest only when diagnosing problems.")
logging.info("log_level::INFO  -  message::Confirmation that things are working as expected.")
logging.warning("log_level::WARNING  -  message::An indication that something unexpected happened, or indicative of some problem in the near future (e.g. ‘disk space low’). The software is still working as expected.")
logging.error("log_level::ERROR  -  message::Due to a more serious problem, the software has not been able to perform some function.")
logging.critical("log_level::CRITICAL  -  message::A serious error, indicating that the program itself may be unable to continue running.")
```

### 1행: logging 모듈 가져오기

1행에서 logging 모듈을 가져옵니다.

### 4행: logging 모듈의 기본 설정하기

4행에서는 logging 모듈의 basicConfig 함수를 통해 기본 설정을 진행합니다.

- `filename` : 로그파일의 이름입니다.
- `encoding` : 로그파일의 인코딩 방식입니다.
- `level` : 로그를 작성할 레벨입니다. 여기서는 `DEBUG`부터 로그를 작성합니다.

### 6~10행: 실제 log 남기기

마지막으로 10~14행에 걸쳐 실제로 log를 수행합니다.

5개의 기본 레벨에 해당하는 로그를 작성합니다.

---
## 기본: logging 모듈의 구조

### logging level

다음 표는 파이썬 공식 문서에서 발췌했습니다.

| 수준       | 사용할 때                                                                                                                                 |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `DEBUG`    | 상세한 정보. 보통 문제를 진단할 때만 필요합니다.                                                                                          |
| `INFO`     | 예상대로 작동하는지에 대한 확인.                                                                                                          |
| `WARNING`  | 예상치 못한 일이 발생했거나 가까운 미래에 발생할 문제(예를 들어 ‘디스크 공간 부족’)에 대한 표시. 소프트웨어는 여전히 예상대로 작동합니다. |
| `ERROR`    | 더욱 심각한 문제로 인해, 소프트웨어가 일부 기능을 수행하지 못했습니다.                                                                    |
| `CRITICAL` | 심각한 에러. 프로그램 자체가 계속 실행되지 않을 수 있음을 나타냅니다.                                                                     |

기초의 예시는 logging level을 `DEBUG`로 지정하여 모든 수준을 추적합니다.

하지만 logging level의 기본 수준은 `WARNING`이므로 추가 설정이 없다면 이 수준 미만(`DEBUG`, `INFO`)은 추적하지 않습니다.

### Logger

Logger 객체는 크게 구성과 메시지 전송 메서드로 구분됩니다.

#### 구성 메서드
- `Logger.setLevel():` 추적할 가장 낮은 수준을 설정합니다.
- `Logger.addHandler()`와 `Logger.removeHandler():` Handler 객체를 추가하고 제거합니다.
- `Logger.adeFilter()`와 `Logger.removeFilter():` Filter 객체를 추가하고 제거합니다.

#### 메시지 전송 메서드
- `Logger.debug(),` `Logger.info(),` `Logger.warning(),` `Logger.error(),` `Logger.critical():` 메시지와 메서드 이름에 해당하는 수준의 로그 레코드를 만듭니다. 또한 표준 문자열 치환 방법을 사용할 수 있습니다!
- `Logger.exception():` `Logger.error()`와 비슷하나 Stack Trace를 덤프하는 메시지를 생성합니다.
- `Logger.log():` 인자를 통해 사용자 정의 로그 수준으로 로깅합니다.

### Handler

Handler 객체는 적절한 로그 메시지를 지정된 대상으로 전달하는 역할을 합니다.

예를 들어, 특정 서버의 모든 로그는 파일로 저장하지만 `ERROR` 이상의 오류는 이메일을 보내는 logging이 가능합니다.

#### Handler 종류
개인적으로 사용해본 Handler는 다음과 같으며 이 외에도 다양한 Handler가 존재합니다.

더 많은 Handler는 다음 링크([link](https://docs.python.org/ko/3/howto/logging.html#useful-handlers))를 통해 확인해보세요!

- `FileHandler`: 파일에 메시지를 보냅니다. 로그 파일은 거의 필수적으로 생성하므로 가장 많이 사용했습니다.
- `StreamHandler`: 스트림에 메시지를 보냅니다. 주로 stdout을 지정해 콘솔창으로 확인할 때 사용했습니다.
- `HTTPHandler`: `GET` 또는 `POST` 방식으로 HTTP 서버에 메시지를 보냅니다. Slack의 Web Hook과 연동해서 사용했습니다.
- `SMTPHandler`: 지정된 이메일 주소로 메시지를 보냅니다. 높은 수준의 오류가 발생했을 때 동작하도록 설정했습니다.

#### 구성 메서드
Handler 객체의 구성 메서드는 다음과 같습니다.

- `setLevel()`: Logger 객체와 마찬가지로 목적지로 보내는 가장 낮은 수준을 지정합니다.
- `setFormatter()`: Handler가 사용할 Formatter 객체를 선택합니다.
- `addFilter()`와 `removeFilter()`: Handler에서 Filter 객체를 추가하고 제거합니다.

#### 예시 코드: 실행 화면으로 logging
```python
import sys
import logging


# Logger 객체 생성
command_logger = logging.getLogger("command")
# 실행 화면(stdout)으로 지정한 Handler 객체 생성
stdout_handler = logging.StreamHandler(sys.stdout)

command_logger.setLevel(logging.DEBUG)
# Logger 객체에 Handler 추가
command_logger.addHandler(stdout_handler)

command_logger.debug("Just Debug - 10")
command_logger.info("Just Info - 20")
command_logger.warning("Just Warning - 30")
command_logger.error("Just Error - 40")
command_logger.critical("Just Critical - 50")
```

위 코드는 실행 화면으로 logging 메시지를 보내는 예시입니다.

`StreamHandler` 객체인 `stdout_handler`를 정의하고 이를 `test_logger` 객체에 추가합니다.

### Formatter

Formatter 객체는 로그 메시지의 최종 순서, 구조, 내용을 구성합니다.

#### LogRecord 속성
다음은 제가 주로 사용한 LogRecord 속성입니다.

역시 다양한 속성이 존재하므로 다음 링크([link](https://docs.python.org/ko/3/library/logging.html#logrecord-attributes))를 통해 어떤 속성을 사용할 수 있는지 확인해보세요.

| 어트리뷰트 이름 | 포맷            | 설명                                                                                                                                  |
| --------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| asctime         | `%(asctime)s`   | 사람이 읽을 수 있는 LogRecord 가 생성된 시간입니다. 기본적으로 ‘1234-01-23 12:34:56,789’ 형식입니다 (쉼표 뒤의 숫자는 밀리 초입니다). |
| name            | `%(name)s`      | 로깅 호출에 사용된 로거의 이름입니다.                                                                                                 |
| levelname       | `%(levelname)s` | 메시지의 텍스트 로깅 수준입니다.                                                                                                      |
| message         | `%(message)s`   | 로그 된 메시지. `msg % args` 로 계산됩니다.                                                                                           |

#### 예시 코드: 나만의 포맷으로 logging
```python
import sys
import logging


format_logger = logging.getLogger("format")
stdout_handler = logging.StreamHandler(sys.stdout)
# Formatter 객체 생성
custom_format = logging.Formatter("%(asctime)s - %(levelname)s - custom:: %(message)s")

# Formatter 객체를 Handler 객체에 추가
stdout_handler.setFormatter(custom_format)

format_logger.setLevel(logging.DEBUG)
format_logger.addHandler(stdout_handler)

format_logger.debug("Just Debug - 10")
format_logger.info("Just Info - 20")
format_logger.warning("Just Warning - 30")
format_logger.error("Just Error - 40")
format_logger.critical("Just Critical - 50")
```

이전 코드에서 `custom_format` 을 이용해 자체 포맷을 지정했습니다.

로그 메시지가 다음과 같이 바뀌었습니다.

- 기존: `Just Debug - 10`
- 포맷 지정: `2023-05-22 18:09:19,665 - DEBUG - custom:: Just Debug - 10`

---
## 응용: 기본기를 탄탄하게!

### 01. 다양한 곳에 logging하기

#### 상황

실행 화면과 파일로 로그를 작성하고 싶습니다.

파일에는 모든 로그 정보를 저장하고, 실행 화면에는 `ERROR` 이상의 심각한 로그만 보여주고자 합니다.

이러한 경우에는 어떻게 처리할 수 있을까요??

#### 예시 코드
```python
import sys
import logging


multiple_logger = logging.getLogger("multiple")

# 실행 화면과 파일로 로그를 작성하는 Handler 객체 2개 생성
stdout_handler = logging.StreamHandler(sys.stdout)
file_handler = logging.FileHandler(filename="04_multiple_handler.log", encoding="utf-8")

# 실행 화면과 파일 Handler의 Formatter 지정
stdout_format = logging.Formatter("%(asctime)-12s - [%(levelname)-8s] - !!! PLEASE CHECK !!! %(message)s !!!")
file_format = logging.Formatter("%(asctime)-12s - [%(levelname)-8s] - %(message)s")

# 각 Handler의 레벨 지정 및 Formatter 지정
stdout_handler.setFormatter(stdout_format)
stdout_handler.setLevel(logging.ERROR)
file_handler.setFormatter(file_format)
file_handler.setLevel(logging.DEBUG)

multiple_logger.setLevel(logging.DEBUG)
multiple_logger.addHandler(stdout_handler)
multiple_logger.addHandler(file_handler)

multiple_logger.info("Infomation")
multiple_logger.error("?? Unknown Memory Error ??")
```

2개의 `Handler` 객체를 생성하고 각 `Handler` 객체마다 `Formatter`를 지정합니다.

이 `Handler` 객체들을 `multiple_logger` 에 추가하면 원하는 로그를 얻을 수 있습니다.

### 02. logging 설정 파일 만들기

#### 상황
열심히 logging을 공부해서 코드를 작성했습니다.

이 로그 설정을 다른 프로그램에서도 사용하고 싶습니다.

해당 코드를 패키지화해서 사용할 수도 있지만 log의 설정 파일을 생성할 수는 없는 걸까요??

#### 예시 코드
- logging.conf
    ```text
    [loggers]
    keys=root,multiple

    [handlers]
    keys=stdoutHandler,fileHandler

    [formatters]
    keys=stdoutFormatter,fileFormatter

    [logger_root]
    level=DEBUG
    handlers=

    [logger_multiple]
    level=DEBUG
    handlers=stdoutHandler,fileHandler
    qualname=multiple

    [handler_stdoutHandler]
    class=StreamHandler
    level=ERROR
    formatter=stdoutFormatter
    args=(sys.stdout,)

    [handler_fileHandler]
    class=FileHandler
    level=DEBUG
    formatter=fileFormatter
    kwargs={"filename": "05_multiple_handler_with_conf.log", "encoding": "utf-8"}

    [formatter_stdoutFormatter]
    format=%(asctime)-12s - [%(levelname)-8s] - !!! PLEASE CHECK !!! %(message)s !!!

    [formatter_fileFormatter]
    format=%(asctime)-12s - [%(levelname)-8s] - %(message)s
    ```

- 파이썬 코드
    ```python
    import logging
    import logging.config


    logging.config.fileConfig("logging.conf")
    multiple_logger = logging.getLogger("multiple")

    multiple_logger.info("Infomation")
    multiple_logger.error("?? Unknown Memory Error ??")
    ```

`conf` 파일을 생성해서 `logging.config.fileConfig()` 함수로 불러올 수 있습니다.

세부적으로는 다음과 같은 항목이 필요합니다.

- \[`loggers`\], \[`handlers`\], \[`formatter`\]: 해당하는 객체들의 나열
- \[`클래스명_객체명`\]: 해당하는 객체에 대한 설정

참고로 `json` 또는 `yaml` 형식의 key-value 설정 파일을 `logging.config.dictConfig()`로 읽어올 수도 있습니다.

- yaml 예시
    ```yaml
    version: 1
    disable_existing_loggers: true
    formatters:
    stdoutFormatter:
        format: '%(asctime)-12s - [%(levelname)-8s] - !!! PLEASE CHECK !!! %(message)s !!!'
    fileFormatter:
        format: '%(asctime)-12s - [%(levelname)-8s] -  %(message)s'
    handlers:
    stdoutHandler:
        class: logging.StreamHandler
        formatter: stdoutFormatter
        level: ERROR
        formatter: stdoutFormatter
        stream  : ext://sys.stdout
    fileHandler:
        class : logging.FileHandler
        formatter: fileFormatter
        level: DEBUG
        filename: 06_multiple_handler_with_yaml.log
    loggers:
    multiple:
        level: DEBUG
        handlers: [stdoutHandler,fileHandler]
        propagate: no
    root:
    level: DEBUG
    handlers: [stdoutHandler,fileHandler]
    ```

---
## 마무리하며

이번 글에서는 파이썬의 logging에 대해 알아보았습니다.

단순하게 로그를 알아보기 위해 공부했는데 그 내용이 생각보다 방대해서 놀라웠습니다.

이 글에서도 파이썬 기본 라이브러리의 기초만 다루고 있습니다.

기본 라이브러리의 심화 내용은 파이썬 공식문서의 Logging Cookbook([link](https://docs.python.org/ko/3/howto/logging-cookbook.html))에 존재합니다...만 아직 공부할 거리가 많다고 느껴집니다...

또한 기본 라이브러리 외에서 가장 많은 star를 받은 loguru 라이브러리(https://github.com/Delgan/loguru)나 크로스플랫폼을 지원하는 모니터링 서비스 Sentry(공식 페이지-[link](https://sentry.io/welcome/), Getting Started with Python-[link](https://docs.sentry.io/platforms/python/?original_referrer=https%3A%2F%2Fgithub.com%2Fgetsentry%2Fsentry)) 등이 존재합니다.

이 글이 조금이나마 도움이 되셨으면 합니다.

감사합니다. 😀

---
## 참고 문헌

- 로그파일, [https://ko.wikipedia.org/wiki/로그파일](https://ko.wikipedia.org/wiki/로그파일)
- logging — 파이썬 로깅 시설, [https://docs.python.org/ko/3/library/logging.html](https://docs.python.org/ko/3/library/logging.html)
- logging.config — 로깅 구성, [https://docs.python.org/ko/3/library/logging.config.html](https://docs.python.org/ko/3/library/logging.config.html)
- 로깅 HOWTO, [https://docs.python.org/ko/3/howto/logging.html](https://docs.python.org/ko/3/howto/logging.html)
- Delgan_loguru_ Python logging made (stupidly) simple, [https://github.com/Delgan/loguru](https://github.com/Delgan/loguru)
- Sentry, [https://sentry.io/welcome/](https://sentry.io/welcome/)
- Sentry - Getting Started with Python, [https://docs.sentry.io/platforms/python/?original_referrer=https%3A%2F%2Fgithub.com%2Fgetsentry%2Fsentry](https://docs.sentry.io/platforms/python/?original_referrer=https%3A%2F%2Fgithub.com%2Fgetsentry%2Fsentry)
