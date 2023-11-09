---
date: 2023-01-27 19:54:00 +/-0900
title: "[Python] Python Interpreter 동작 방식 살펴보기"
categories: [Develop, python]
tags: [개발(develop), 파이썬(python), 인터프리터(interpreter), 컴파일러(compiler), ast(abstract_syntax_tree), 가상머신(virtual_machine), 컴파일타임(compile_time), 런타임(runtime)]

---
## 개요
안녕하세요.

이번 글에서는 Python의 Interpreter를 살펴보는 시간을 가져보겠습니다.

각각의 단계를 살펴보고 어떤 역할을 하는지 확인합니다.

또한 인터프리터와 관련된 간단한 이야기거리를 나눕니다.

참고로 현재 글은 CPython(Cython)을 바탕으로 작성되었으며 Pypy, Jython 등은 다른 동작을 보일 수 있습니다.

---
## 파이썬 인터프리터의 동작 방식

### 전체 과정

![파이썬 인터프리터](/assets/img/develop/3005/3005_01_python_intepreter.png)
_[그림01] 파이썬 인터프리터_

파이썬 인터프리터는 5가지 작업(Lexing, Parsing, Compiling, Interpreting, Running)을 수행합니다.

각 작업에 대해서 살펴보겠습니다.

### ① Lexing: 각 라인을 어휘 분석 후 토큰으로

소스코드를 읽은 인터프리터는 가장 먼저 Lexing이라는 작업을 수행합니다.

이는 소스코드 각 라인의 어휘 분석을 진행하고 토큰화하는 과정입니다.

### ② Parsing: 토큰을 AST 구조로

Lexing이 완료되면 다음 단계는 Parsing입니다.

인터프리터의 Parser는 토큰에서 AST(Abstract Syntax Tree, 추상 구문 트리)라는 구조를 생성합니다.

AST에 대해 조금 더 공부가 필요하다고 느껴 우선은 다음의 링크로 대체합니다.

- 위키백과의 추상 구문 트리 문서, [https://ko.wikipedia.org/wiki/추상_구문_트리](https://ko.wikipedia.org/wiki/추상_구문_트리)
- 파이썬에서의 AST, [https://kamneemaran45.medium.com/python-ast-5789a1b60300](https://kamneemaran45.medium.com/python-ast-5789a1b60300)

### ③ Compiling: AST를 바이트코드로

3번째 단계는 Compiling에서는 AST를 Bytecode로 변환합니다.

여기서 바이트코드란 "특정 하드웨어가 아닌 가상 컴퓨터에서 돌아가는 실행 프로그램을 위한 표현법"을 의미합니다.

Python에서는 Python 전용 가상머신의 표현법으로 변환한다고 생각하시면 됩니다.

추가로 이 바이트코드는 `.pyc` 확장자를 가지며 `__pycache__` 디렉터리에 저장됩니다.

### ④ Interpreting: 각 바이트코드의 실행

위의 바이트코드는 Interpreting을 거칩니다.

여기서 바이트코드를 해석하는 PVM(Python Virtual Machine)이 등장합니다.

인터프리터는 기계어를 load한 뒤 이를 PVM에 입력합니다.

이를 바탕으로 PVM은 바이트코드는 실행 코드로 변환합니다.

### ⑤ Running: 실제 기계 코드의 실행

마지막 단계는 Running입니다.

실행 코드를 직접 메모리에 올리고 실제로 실행합니다!

---

## 관련된 이야기거리

### ① 파이썬은 인터프리터 언어일까? 컴파일 언어일까?

흔히 파이썬은 인터프리터 언어로 알려져있습니다.

하지만 위의 동작 과정을 살펴보면 파이썬 인터프리터에는 컴파일이 포함되어 있습니다.

그렇다면 파이썬은 인터프리터 언어가 아닌 걸까요?

관련된 글 중 [좋은 내용을 담은 글](https://soooprmx.com/파이썬은-인터프리터언어입니까/)을 찾아 공유드립니다.

해당 내용을 직접 확인해보세요!

### ② 컴파일 에러와 런타임 에러의 차이

파이썬을 실행하면 다양한 오류에 부딪힙니다.

파이썬 공식 문서에서는 다양한 오류들을 크게 문법 에러와 예외로 구분해두었습니다.

이는 각각 컴파일타임 에러와 런타임 에러에 대응된다고 생각합니다.

두 에러는 다음과 같은 특징을 가집니다.

구분|문법 에러(컴파일타임 에러)|예외(런타임 에러)
---|:---:|:---:
발생시점|컴파일타임(③)|런타임(⑤)
특징|파이썬 문법의 오류|실행 과정에서 확인되는 오류
예시|`print("Hello, World!"`|`a = 10 / 0`

---
## 마무리하며

이 글에서는 파이썬의 인터프리터를 살펴보았습니다.

컴파일타임 에러와 런타임 에러를 찾다보니 파이썬 인터프리터의 동작방식까지 확인하게 되었습니다... 하하

그래도 인터프리터의 각 단계에 대해 명확하게 확인할 수 있었고 또한 인터프리터 언어와 컴파일러 언어의 구분에 대해서도 생각해볼 수 있는 좋은 기회였습니다.

여러분들에게도 이 글이 작게나마 도움이 되었으면 합니다.

감사합니다. 😀

---
## 참고 문헌
- 에러와 예외, [https://docs.python.org/ko/3/tutorial/errors.html](https://docs.python.org/ko/3/tutorial/errors.html)
- 바이트코드, [https://ko.wikipedia.org/wiki/바이트코드](https://ko.wikipedia.org/wiki/바이트코드)
- 추상 구문 트리, [https://ko.wikipedia.org/wiki/추상_구문_트리](https://ko.wikipedia.org/wiki/추상_구문_트리)
- What is Python? How the Interpreter Works and How to Write "Hello World" in Python, [https://www.freecodecamp.org/news/what-is-python-beginners-guide/](https://www.freecodecamp.org/news/what-is-python-beginners-guide/)
- Python AST, [https://kamneemaran45.medium.com/python-ast-5789a1b60300](https://kamneemaran45.medium.com/python-ast-5789a1b60300)
- 파이썬은 인터프리터언어입니까?, [https://soooprmx.com/파이썬은-인터프리터언어입니까/](https://soooprmx.com/파이썬은-인터프리터언어입니까/)
- Python Interpreter - Introduction, [https://youtu.be/VsjJfaUdFO8](https://youtu.be/VsjJfaUdFO8)
