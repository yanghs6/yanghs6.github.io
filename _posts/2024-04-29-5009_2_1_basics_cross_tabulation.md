---
date: 2024-04-29 20:53:00 +/-0900
title: "[Data] [빅데이터를 지탱하는 기술] 정리하기 - 2-1 크로스 집계의 기본"
categories: [Data, concept]
tags: [데이터(data), 개념(concept), 크로스테이블(cross_table), 트랜잭션테이블(transaction_table), 룩업테이블(lookup_table), 피벗(pivot)]
description: 빅데이터를 지탱하는 기술 시리즈로 2장 1절, 크로스 집계의 기본을 정리했습니다.

---
## 개요

- [빅데이터를 지탱하는 기술] 정리하기 시리즈
    1. 1장 빅데이터의 기초 지식
        1. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 1-1 [배경] 빅데이터의 정착]({% post_url 2023-03-12-5001_1_1_bigdata_history %})
        2. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 1-2 빅데이터 시대의 데이터 분석 기반]({% post_url 2023-03-19-5002_1_2_analysis_based_data %})
        3. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 1-3 [속성 학습] 스크립트 언어에 대한 특별 분석과 데이터 프레임]({% post_url 2023-03-26-5003_1_3_script_language_and_dataframe %})
        4. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 1-4 BI 도구와 모니터링]({% post_url 2023-04-09-5004_1_4_BI_tool_and_monitoring %})
    2. 2장 빅데이터의 탐색
        1. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 2-1 크로스 집계의 기본]({% post_url 2024-04-29-5009_2_1_basics_cross_tabulation %})
        2. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 2-2 열 지향 스토리지에 의한 고속화]({% post_url 2024-05-17-5010_2_2_columnar_storage %})
        3. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 2-3 애드 혹 분석과 시각화 도구]({% post_url 2024-09-21-5011_2_3_ad_hoc_visualization %})
        4. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 2-4 데이터 마트의 기본 구조]({% post_url 2024-12-06-5012_2_4_architecture_data_mart %})
    3. 3장 빅데이터의 분산 처리
        1. [[Data] "빅데이터를 지탱하는 기술" 정리하기 - 3-1 대규모 분산 처리의 프레임워크]({% post_url 2025-01-08-5013_3_1_large_distribute_process %})

안녕하세요.

이번 글에서는 "빅데이터를 지탱하는 기술" 이라는 책의 2장 1절, "크로스 집계의 기본"을 정리했습니다.

---
## 트랜잭션 테이블, 크로스 테이블, 피벗 테이블 - ‘크로스 집계’의 개념

크로스 집계에 앞서 우선 테이블에 대해 알아야합니다.

**<u>크로스 테이블(Cross Table)</u>**은 행과 열이 교차하는 부분에 숫자를 삽입한 테이블입니다.

아래 그림에서 ❶에 해당하는 항목으로 스프레드시트에서 이러한 보고서 직접 작성합니다.

**<u>트랜잭션 테이블(Transaction Table)</u>**은 행 방향으로 데이터가 증가하며, 열 방향은 고정된 테이블입니다.

데이터베이스에서 처리하기 쉽고 아래 그림 중 ❷ 그림입니다.

그렇다면 **<u>크로스 집계(Cross Tabulation)</u>**란 무엇일까요??

크로스 집계는 트랜잭션 테이블을 크로스 테이블로 변환하는 과정입니다.

소량의 데이터를 크로스 집계할 때는 스프레드시트의 피벗 테이블(Pivot Table) 기능을 활용할 수 있습니다!

피벗에 대한 보다 자세한 설명은 글 후반부에 담겨있습니다 :)

아래 그림은 상품을 행, 매출월을 열로 하는 크로스 테이블과 트랜잭션 테이블을 보여줍니다.

- [그림 2-1] 크로스 테이블과 트랜잭션 테이블
    - ❶ 크로스 테이블

        |        | 2017년 1월 | 2017년 2월 | 2017년 3월 |
        | ------ | ---------- | ---------- | ---------- |
        | 상품 A | 57500      | 57500      | 60000      |
        | 상품 B | 2400       | 5800       | 12400      |
    - ❷ 트랜잭션 테이블

        | 매출 월    | 상품명 | 금액  |
        | ---------- | ------ | ----- |
        | 2017년 1월 | 상품 A | 57500 |
        | 2017년 1월 | 상품 B | 2400  |
        | 2017년 2월 | 상품 A | 57500 |
        | 2017년 2월 | 상품 B | 5800  |
        | 2017년 3월 | 상품 A | 60000 |
        | 2017년 3월 | 상품 B | 12400 |

### 피벗 테이블 기능에 의한 크로스 집계

피벗 테이블에서는 형과 열이 교차하는 부분 값이 자동 집계됩니다.

따라서 합계, 평균, 최댓값, 이전 값과의 차이 등을 계산할 수 있습니다.

또한 행과 열의 항목을 바꿔 집계 결과를 즉시 확인 가능합니다.

이러한 크로스 테이블을 그래프로 시각화하는 기능이 바로 피벗 그래프(Pivot Graph)입니다.

---
## 룩업 테이블 - 테이블을 결합하여 속성 늘리기

**<u>룩업 테이블(Lookup Table)</u>**은 트랜잭션 테이블에 새로운 항목을 더하는 대신 다른 테이블과 결합할 때 사용합니다.

트랜잭션 테이블과 룩업 테이블은 독립적으로 관리할 수 있습니다.

트랜잭션 테이블은 업무 데이터베이스에서 로드하고 룩업 테이블은 데이터 분석 용도에 따라 변경하는 것이죠.

### BI 도구에 의한 크로스 집계

데이터 분석이 많다면 스프레드시트 대신 BI 도구를 활용하는 방안을 고려할 수 있습니다.

### Pandas에 의한 크로스 집계

파이썬의 Pandas를 통해 크로스 집계도 가능한데요.

아래는 그 예시입니다.

1. 스크립트로 크로스 집계를 원한다면 pandas 이용
    ```python
    In [1]: import pandas as pd
    In [2]: df1 = pd.read_excel(u'판매 데이터.xlsx', u'판매 이력')
    In [3]: df2 = pd.read_excel(u'판매 데이터.xlsx', u'상품')
    In [4]: df3 = pd.merge(df1, df2, on=u'상품 ID')
    In [5]: df3
    Out[5]:
             매출일   점포 ID   상품 ID   고객 ID    금액  상품명   상품 카테고리
    0  2017-01-01       11      101     1001  57500  상품 A  식료품
    1  2017-02-01       12      101     1003  57500  상품 A  식료품
    2  2017-03-01       12      101     1003  60000  상품 A  식료품
    3  2017-01-01       11      102     1002   2400  상품 B  전자제품
    4  2017-02-01       11      102     1002   5800  상품 B  전자제품
    5  2017-03-01       11      102     1002  12400  상품 B  전자제품
    ```

2. 컬럼 추가 후 pivot_table()로 크로스 집계 수행
    
    ```python
    In [6]: df3.pivot_table(u'금액',[u'점포 ID', u'상품명'], u'매출일', aggfunc='num')
    Out[6]:
    매출일           2017-01-01  2017-02-01  2017-03-01
    점포 ID   상품명
    11       상품 A       57500         NaN         NaN
             상품 B        2400        5800       12400
    12       상품 A         NaN       57500       60000
    ```

3. pandas 에서는 데이터 프레임으로 어떤 데이터라도 결합 가능
    - read_csv()로 CSV 파일 로드
    - read_clipboard()로 클립보드로부터 복사
    
    ```python
    In [7]: def category(row):
                return {101:u'식료품'}.get(row[u'상품 ID'], u'그 외')
    In [8]: df1.head(2)
    Out[8]:
             매출일   점포 ID   상품 ID    고객 ID    금액  상품 카테고리
    0  2017-01-01       11      101     1001  57500  식료품
    1  2017-01-01       11      102     1002   2400  그 외
    ```

---
## SQL에 의한 테이블 집계 - 대량 데이터의 크로스 집계 사전 준비

수백만 이상 데이터는 SQL을 사용해 데이터 집계(aggregation)하는데요.

그 중에서도 집계 함수(aggregation function)을 이용한다면 데이터양이 줄어듭니다.

다음은 판매 이력을 저장한 업무 데이터베이스의 예시입니다.

전체 데이터를 모두 꺼내 집계하는 대신 SQL로 집계하고 크로스 집계를 수행해 효율성을 챙길 수 있습니다.

- [그림 2-2] SQL에 의한 데이터 집계
   ```sql
   postgres~# SELECT date_trunc('month', "매출일")::DATE AS "매출일",
   postgres~#        "점포 ID",
   postgres~#        "상품 ID",
   postgres~#        "고객 ID",
   postgres~#        sum("금액") AS "금액"
   postgres~# FROM   "판매 이력"
   postgres~# GROUP BY 1, 2, 3, 4
   postgres~# 
      매출일   | 점포 ID | 상품 ID | 고객 ID | 금액
   ---------------------------------------------
   2017-03-01|     12 |    101 |   1003 | 60000
   2017-01-01|     11 |    101 |   1001 | 57500
   2017-01-01|     11 |    102 |   1002 |  2400
   2017-02-01|     11 |    102 |   1002 |  5800
   2017-02-01|     12 |    101 |   1003 | 57500
   2017-03-01|     11 |    102 |   1002 | 12400
   (6 rows)
   ```

실행 결과를 보면 크로스 테이블이 아니라 트랜잭션 테이블 형태를 크로스 집계하는데요.

이 책에서는 이를 SQL로 집계 → 시각화 도구로 크로스 집계 하는 절차로 분류합니다.

전자는 ❶ 데이터 집계의 프로세스, 후자는 ❷ 시각화 프로세스에 해당합니다.

![[그림 2-3] 데이터를 집계해서 시각화하기](/assets/img/data/5009/5009_01_aggregate_visualize_data.png)
_[그림 2-3] 데이터를 집계해서 시각화하기_

### 테이블 종횡 변환 - SQL 편

종방향 테이블은 다음 그림과 같이 다수의 행으로, 횡방향 테이블은 다수의 컬럼으로 구성된 테이블입니다.

종방향 테이블이 트랜잭션, 횡방향 테이블이 크로스 테이블이라면 두 테이블은 크로스 집계에 의해 변활할 수 있습니다.

- [그림 C2-1] 종방향 테이블과 횡방향 테이블
    - ❶ 종방향 테이블(Vertical): vtable

        | Uid | Key | value |
        | --- | --- | ----- |
        | 101 | c1  | 11    |
        | 101 | c2  | 12    |
        | 101 | c3  | 13    |
        | 102 | c1  | 21    |
        | 102 | c2  | 22    |
        | 102 | c3  | 23    |

    - ❷ 트랜잭션 테이블(Horizon): htable

      | Uid | c1  | c2  | c3  |
      | --- | --- | --- | --- |
      | 101 | 11  | 12  | 13  |
      | 102 | 21  | 22  | 23  |

한편, 앞서 나온 피벗이라는 개념을 여기서 소개하는데요.

종방향 테이블과 횡방향 테이블의 상호 변환을 테이블의 종횡 변환 또는 **<u>피벗(Pivot)</u>**이라고 합니다.

표준 SQL의 경우 다음 쿼리를 통해 피벗이 가능합니다.

하지만 이를 지원하는 특수한 구문이 없는 한 컬럼의 수만큼 SQL이 늘어난다는 단점이 있습니다.

```sql
postgres~# SELECT uid,
postgres~#        sum(CASE WHEN key = 'c1' THEN value END) AS c1,
postgres~#        sum(CASE WHEN key = 'c2' THEN value END) AS c2,
postgres~#        sum(CASE WHEN key = 'c3' THEN value END) AS c3
postgres~# FROM   vtable
postgres~# GROUP BY uid
postgres~# ;
    uid | c1 | c2 | c3 
    --------------------
    101 | 11 | 12 | 13
    102 | 21 | 22 | 23
    (2 rows)
```

피벗이 있다면 반대의 언피벗(Unpivot) 역시 존재합니다.

횡방향에서 종방향으로 변환하는 작업인데요.

다음은 이를 SQL로 실행한 결과입니다.

```sql
postgres~# SELECT uid, 'c1' AS key, c1 AS value FROM htable
postgres~# UNION ALL
postgres~# SELECT uid, 'c2' AS key, c2 AS value FROM htable
postgres~# UNION ALL
postgres~# SELECT uid, 'c3' AS key, c3 AS value FROM htable
postgres~# ;
    uid | key | value
    -------------------
    101 | c1  |    11
    102 | c1  |    21
    101 | c2  |    12
    102 | c2  |    22
    101 | c3  |    13
    102 | c3  |    23
    (6 rows)
```

모든 컬럼명을 적어야하지만  `unnest` 함수를 이용해 조금 더 간결하게 표현할 수 있습니다.

```sql
postgres~# SELECT t1.uid, t2.key, t2.value FROM htable t1
postgres~# CROSS JOIN unnest(
postgres~#   array['c1', 'c2', 'c3'],
postgres~#   array[c1, c2, c3],
postgres~# ) t2 (key, value)
postgres~# ;
    uid | key | value
    -------------------
    101 | c1  |    11
    102 | c1  |    21
    101 | c2  |    12
    102 | c2  |    22
    101 | c3  |    13
    102 | c3  |    23
    (6 rows)
```

### 테이블 종횡 변환 - pandas 편

먼저 SQL로 집계한 종방향 테이블을 준비하겠습니다.

```python
In [1]: query = '''
        : SELECT uid, key, sum(value) value FROM vtable GROUP BY 1, 2
        : '''
        : vtable = pd.read_sql(query, engine)
        : vtable
Out[1]:
   uid  key  value
1  101   c1     11
2  101   c2     12
3  101   c3     13
4  102   c1     21
5  102   c2     22
6  102   c3     23
```

피벗을 원한다면 `pivot()`를 이용합니다.

컬럼명은 자동 생성으로 생성되며 칼럼이 너무 많다면 대량의 메모리와 CPU를 소비해 프로세스를 정지시킬 수 있습니다.

```python
In [2]: vtable.pivot('uid', 'key', 'value')
Out[2]:
key  c1  c2  c3
uid
101  11  12  13
102  21  22  23

In [3]: vtable.pivot_table('value', ['uid'], ['key'], aggfunc='sum')
Out[3]:
key  c1  c2  c3
uid
101  11  12  13
102  21  22  23
```

언피벗은 `melt()`를 통해 가능합니다.

```python
In [1]: htable
Out[1]:
key  c1  c2  c3
uid
101  11  12  13
102  21  22  23

In [2]: htable.melt('uid', var_name='key', value_name='value')
Out[2]:
   uid  key  value
0  101   c1     11
1  101   c2     12
2  101   c3     13
3  102   c1     21
4  102   c2     22
5  102   c3     23
```

SQL에 비해 비교적 간단하게 피벗과 언피벗을 수행해봤습니다.

---
## 데이터 집계 → 데이터 마트 → 시각화 - 시각화 구성은 데이터 마트의 크기에 따라 결정된다

데이터 집계와 시각화 사이에는 데이터 마트가 존재합니다.

데이터 마트가 작을수록 시각화는 간단해지지만 원래 데이터의 정보가 사라져 시각화 프로세스에서 표현 범위가 줄어듭니다.

반대로 많은 정보를 유지한다면 데이터 마트가 거대해져 좋은 시각화가 불가능합니다.

그러므로 데이터 양과 시각화는 서로 반비례 관계라고 할 수 있습니다.

만약 데이터 양을 수백만 건 정도로 줄일 수 있다면 모든 데이터를 시각화 도구에 업로드할 수 있으나 이를 넘어선다면 지연이 적은 데이터베이스에 데이터 마트 구축이 필요합니다.

---
## 마무리하며

이번 글에서는 크로스 집계의 기본에 대해 살펴보았습니다.

가장 먼저 행과 열이 교차하는 크로스 테이블과 행 방향으로 데이터가 증가하는 트랜잭션 테이블을 분류했습니다.

트랜잭션 테이블을 크로스 테이블로 변환하는 것을 크로스 집계라고 하구요.

룩업 테이블은 트랜잭션 테이블과 결합해 사용하는 테이블입니다.

이후 SQL과 파이썬의 Pandas를 이용해 실제 크로스 집계인 피벗과 언피벗을 수행했습니다.

마지막으로 테이터 집계 시 유의해야하는 데이터 양과 시각화의 관계를 알아봤습니다.

2장 1절의 용어는 이후에도 계속 나오니 꼭꼭 기억하시길 바랍니다!

이 글이 조금이나마 도움이 되셨으면 합니다.

감사합니다. 😀

---
## 참고 문헌

- 니시다 케이스케, *빅데이터를 지탱하는 기술*, 제이펍, 2018
