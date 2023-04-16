---
date: 2023-03-26 22:26:00 +/-0900
title: "[Data] [빅데이터를 지탱하는 기술] 정리하기 - 1-3 [속성 학습] 스크립트 언어에 대한 특별 분석과 데이터 프레임"
categories: [Data, concept]
tags: [데이터(data), 개념(concept), 파이썬(python), 데이터프레임(data_frame), 전처리(preprocessing), 정규표현(regular_expression), adhoc, r, sql]

---
## 개요

- [빅데이터를 지탱하는 기술] 정리하기 시리즈
  1. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 1-1 [배경] 빅데이터의 정착]({% post_url 2023-03-12-5001_1_1_bigdata_history %})
  2. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 1-2 빅데이터 시대의 데이터 분석 기반]({% post_url 2023-03-19-5002_1_2_analysis_based_data %})
  3. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 1-3 [속성 학습] 스크립트 언어에 대한 특별 분석과 데이터 프레임]({% post_url 2023-03-26-5003_1_3_script_language_and_dataframe %})
  4. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 1-4 BI 도구와 모니터링]({% post_url 2023-04-09-5004_1_4_BI_tool_and_monitoring %})

안녕하세요.

이번 글에서는 "빅데이터를 지탱하는 기술" 이라는 책의 1장 3절, "[속성 학습] 스크립트 언어에 대한 특별 분석과 데이터 프레임"을 정리했습니다.

---
## 데이터 처리와 스크립트 언어 - 인기 언어인 파이썬과 데이터 프레임

데이터 분석을 위해서는 데이터 수집이 필요합니다.

데이터의 종류는 파일 서버에서 다운로드하거나, 인터넷 경유한 API로부터 얻거나 또는 읽을 수 없어 **<u>전처리(preprocessing)</u>**가 필요한 데이터 등 다양합니다.

이러한 데이터 분석에 사용되는 스크립트 언어는 R과 Python이 존재합니다.

데이터 엔지니어 사이에서는 Python의 인기가 높은데 다음의 이유 때문입니다.

- 통계 분석에 특화된 R 대비 범용 언어로 발전
- 다양한 분야의 라이브러리 존재
- NumPy, SciPy라는 수치 계산용 라이브러리와 머신러닝의 프레임워크 충실
- 데이터 처리에는 R에서의 데이터 프레임 모델을 Python으로 만든 pandas 사용

---
## 데이터 프레임, 기초 중의 기초 - 배열 안의 배열로부터 작성

**<u>데이터 프레임(Data Frame)</u>**은 표 형식의 데이터를 추상화한 객체입니다.

스프레드시트 중 하나의 시트 혹은 DB 중 하나의 테이블을 객체로 취급하는 셈입니다.

표 형식의 데이터는 가로와 세로의 2차원으로 나누어져있고, 이를 배열 안의 배열로 준비한다면 데이터 프레임을 생성할 수 있습니다.

```python
In [1]: import pandas as pd
      : pd.DataFrame([['2022-01-01', 'x', 1], ['2022-01-02', 'y', 2]])
Out[1]:
            0  1  2
0  2020-01-01  x  1
1  2020-01-01  y  2
```

---
## 웹 서버의 액세스 로그의 예 - pandas의 데이터 프레임으로 간단히 처리

예시로 리스트 1-1과 같은 웹서버의 액세스 로그는 그대로 읽는 것이 불가능합니다.

[리스트 1-1]
```python
x.x.x.x - - [01/Jul/1995:00:00:01 -0400] "GET /history/..." 200 6245
x.x.x.x - - [01/Jul/1995:00:00:07 -0400] "POST /school/..." 200 3985
```
출처: https:/ita.ee.lbl.gov/html/contrib/NASA-HTTP.html

우선 이 데이터를 파이썬 정규식 사용해 파싱합니다.

```python
In [2]: import pandas as pd
      :	# 로그 각 행에 매칭되는 정규 표현
      :	pattern = re.compile('^\S+ \S+ \S+ \[(.*)\] "(.*)" (\S+) (\S+)$')
      : # 정규 표현으로 파싱하는 함수
      : def parse_access_log(path):
      :     for line in open(path):
      :         for m in pattern.finditer(line):
      :             yield m.groups()
      : 
      : # 로그 파일을 읽어서 데이터 프레임으로 변환
      : columns = ['time', 'request', 'status', 'bytes']
      : pd.DataFrame(parse_access_log('access.log'), columns=columns)
Out[2]:
                         time                 request  status  bytes
0  01/Jul/1995:00:00:01 -0400  GET /history/apollo...     200   6245
1  01/Jul/1995:00:00:07 -0400  GET /shuttle/countd...     200   3985
```

이제는 다음과 같이 데이터를 가공할 수 있습니다.

```python
# 데이터 프레임을 변수에 보관
In [3]: df = pd.DataFrame(parse_access_log('access.log'), columns=columns)
# 'time' 컬럼 덮어쓰기
In [4]: df.time = pd.to_datetime(df.time, format='%d/%b/%Y:%X', exact=False)
# 결과 확인하기
In [5]: df.head(2)
Out[5]: 
                  time                 request  status  bytes
0  1995-07-01 00:00:01  GET /history/apollo...     200   6245
1  1995-07-01 00:00:07  GET /shuttle/countd...     200   3985
```

마지막으로 결과 데이터를 CSV 파일로 보존합니다.

```python
# CSV 파일로 보관
In [6]: df.to_csv("access_log.csv", index=False)
# 결과 확인하기
In [7]: !head -3 access_log.csv
time,request,status,bytes
1995-07-01 00:00:01,GET /history/apollo/ HTTP/1.0,200,6245
1995-07-01 00:00:07,GET /shuttle/countdown/ HTTP/1.0,200,3985
```

### 데이터의 전처리에 사용할 수 있는 pandas 함수

다음의 표에서 데이터 가공에 자주 사용되는 pandas 함수를 정리해두었습니다.

| 이름   | 설명                              |
| ------ | --------------------------------- |
| ix     | 조건에 일치하는 데이터만을 검색   |
| drop   | 지정한 행(혹은 컬럼)을 삭제       |
| rename | 인덱스 값(혹은 컬럼명)을 변경     |
| dropna | 갑싱 없는 행(혹은 컬럼명)을 제외  |
| fillna | 값이 없는 셀을 지정한 값으로 치환 |
| apply  | 각 칼럼(혹은 각 행)에 함수 적용   |

---
## 시계열 데이터를 대화식으로 집계하기 - 데이터 프레임을 그대로 사용한 데이터 집계

pandas에는 **<u>시계열 데이터(Time-series Data)</u>**을 다루는 여러 기능이 있습니다.

여기서는 시간을 인덱스로 지정하여 앞선 결과 데이터를 집계해보겠습니다.

```python
# CSV 파일 로드(시간으로 파싱)
In [1]: import pandas as pd
      : df1 = pd.read_csv('access_log.csv', parse_dates=['time'])
# 시간을 인덱스로 지정
In [2]: df2 = df1.set_index('time')
# 인덱스에 의한 시간 필터링
In [3]: df3 = df2['1995-07-01': '1995-07-03']
# 1일 분의 액세스 수 카운트
In [4]: df3.resample('id').size()
Out[4]:
time
1995-07-01  64714
1995-07-02  60265
1995-07-03  89584
Freq: D, dtype: int64
```

데이터 프레임 분석은 위와 같이 새로운 변수에 차례대로 값을 대입하면서 데이터를 가공합니다.

애드 혹 분석에서는 이러한 시행착오를 거치며 몇 번이고 데이터 처리 반복합니다.

따라서 이러한 변수를 잘 사용한다면 조금씩 데이터 분석을 진행할 수 있습니다!

### 스몰 데이터 기술 잘 사용하기

pandas는 분산 시스템이 아니므로 빅데이터에 대한 대응이 불가능합니다.

실제로 처리를 시도한다면 메모리 부족, 많은 시간 소요 등의 문제가 발생합니다.

따라서 pandas는 빅데이터 기술과 구분하여 사용해야 합니다.

예를 들어 여러 데이터 소스로부터 데이터 읽고 결합할 떄와 같이요.

또는 SQL과 스크립트 언어를 구분해서 사용하는 처리에도 사용할 수 있습니다.

---
## SQL 결과를 데이터 프레임으로 활용하기

데이터 프레임의 단점으로는 익숙해지기까지 시간이 소요된다는 점입니다.

오히려 SQL에 익숙하다면 SQL 쿼리로 데이터 프레임을 생성할 수 있습니다.

다음은 직전의 집계를 SQL로 작성하는 예시입니다.

SQLite 이용해 테이블을 작성했으며 실행 결과를 real_sql() 함수로 사용합니다.

```python
# 데이터베이스에 접속
In [1]: import pandas as pd
      : import sqlalchemy
      : engine = sqlalchemy.create_engine('sqlite:///sample.db')
# 쿼리를 실행해서 데이터 프레임으로 변환
In [2]: query = '''
      : SELECT substr(time, 1, 10) time, count(*) count
      : FROM   access_log
      : WHERE  time BETWEEN '1995-07-01' AND '1995-07-04'
      : GROUP BY 1 ORDER BY 1
      : '''
      : pd.read_sql(query, engine)
Out[2]:
         time  count
0  1995-07-01  64714
1  1995-07-02  60265
2  1995-07-03  89584
```

### 실행 결과를 확인하는 부분에서 데이터 프레임 사용

이처럼 데이터 프레임은 표 형식의 모든 데이터 처리에 용이합니다.

덕분에 애드혹 데이터 분석에서 스크립트에 의한 데이터 처리에 이르기까지 넓게 이용하고 있습니다.

```sql
SQLite에 의한 테이블 작성

$ sqlite3 sample.db
-- 테이블 작성
SQLite version 3.16.0 2016-11-04 19:09:39
Enter ".help" for usage hints.
sqlite> CREATE TABLE access_log (
   ...>   time timestamp,
   ...>   request text,
   ...>   status bigint,
   ...>   bytes bigint
   ...> );
-- 구분 문자 지정
sqlite> .separator ,
-- CSV 파일로부터 로드
sqlite> .import access_log.csv access_log
```

빅데이터 애드 혹 분석의 기본이 되는 개념은 panda에서 SQL을 실행하는 것과 동일합니다.

데이터 집계에서 데이터 웨어하우스나 데이터 레이크를 이용하고 그 결과를 데이터 프레임으로 변환해두면, 그다음은 스몰 데이터와 마찬가지로 대화형 데이터 확인 및 가공합니다.

---
## 마무리하며

이번 글에서는 스크립트 언어와 데이터 프레임에 대해 간략하게 알아보았습니다.

데이터 처리에는 스크립트 언어인 R과 파이썬이 주로 사용되며 그 중에서도 범용성이 우수한 파이썬의 인기가 높습니다.

데이터 프레임은 표 형식의 데이터를 추상화한 객체로 파이썬, SQL 등의 언어를 통해 생성 및 가공할 수 있습니다.

이러한 스몰 데이터의 처리 방식은 빅데이터에서도 유사하게 적용됩니다.

이 글이 조금이나마 도움이 되셨으면 합니다.

감사합니다. 😀

---
## 참고 문헌

- 니시다 케이스케, *빅데이터를 지탱하는 기술*, 제이펍, 2018
