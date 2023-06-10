---
date: 2023-05-12 16:26:00 +/-0900
title: "[Data] 블로그 데이터 파이프라인 만들기 - 03. GA4와 BigQuery 연결"
categories: [Data, datapipeline]
tags: [데이터(data), 파이프라인(pipeline), 빅쿼리(bigquery), 데이터웨어하우스(data_warehouse), 집계(aggregation)]

---
## 개요

- [블로그 데이터 파이프라인 만들기] 시리즈
  1. [[Data] 블로그 데이터 파이프라인 만들기 - 01. Google Analytics4]({% post_url 2023-04-30-5005_Blog_analysis_01_GA4 %})
  2. [[Data] 블로그 데이터 파이프라인 만들기 - 02. BigQuery]({% post_url 2023-05-07-5006_Blog_analysis_02_bq %})
  3. [[Data] 블로그 데이터 파이프라인 만들기 - 03. GA4와 BigQuery 연결]({% post_url 2023-05-12-5007_Blog_analysis_03_connect_GA4_bq %})

안녕하세요.

이번 글에서는 2023년 계획 중 하나인 "블로그 데이터 파이프라인 만들기" 세번째 시간으로 GA4와 BigQuery를 연결하는 방법에 대해 알아보겠습니다.

---
## 실습

### 1. GA4 스트림 생성

우선 GA4에서 스트림을 생성해야 합니다.

다음과 같은 GA4 화면에서 빨간 박스인 "관리"를 클릭합니다.

![GA4 관리](/assets/img/data/5007/5007_01_ga4_manage.png)
_[그림1] GA4 관리_

여러 설정 중 빨간 박스인 "데이터 스트림"을 클릭합니다.

![GA4 데이터스트림](/assets/img/data/5007/5007_02_ga4_setting_datastream.png)
_[그림2] GA4 데이터스트림_

데이터스트림 화면에서 스트림을 추가합니다.

블로그 페이지는 IOS, AOS, Web 중 Web에 해당하므로 해당 분류를 선택합니다. 

![GA4 데이터스트림 생성](/assets/img/data/5007/5007_03_ga4_create_datastream.png)
_[그림3] GA4 데이터스트림 생성_

마지막으로 웹사이트 URL과 데이터 스트림 이름을 입력하면 데이터 스트림 생성이 완료됩니다.

![GA4 데이터스트림의 URL 및 이름 작성](/assets/img/data/5007/5007_03_ga4_create_datastream.png)
_[그림4] GA4 데이터스트림 URL 및 이름 작성_

데이터 스트림을 확인하고싶다면 데이터 스트림을 클릭해보세요.

직접 입력한 URL과 스트림 이름, 측정ID 같은 지표와 수집할 이벤트 등 다양한 정보를 확인할 수 있습니다.

![GA4 데이터스트림의 URL 및 이름 작성](/assets/img/data/5007/5007_05_ga4_datastream.png)
_[그림5] GA4 데이터스트림 정보 확인_

### 2. GA4 스트림과 BigQuery 연결

GA4 관리 화면에서 제품 링크 - BigQuery 를 선택합니다.

![GA4 BigQuery 링크](/assets/img/data/5007/5007_06_ga4_bigquery_select.png)
_[그림6] GA4 BigQuery 링크_

연결을 클릭해서 BigQuery와 연동하는 절차를 시작합니다.

저는 기존에 연결해둔 링크가 있어서 활성화되어있지 않지만 처음 연결한다면 클릭 가능합니다.

![GA4 BigQuery 링크 연결](/assets/img/data/5007/5007_07_ga4_bigquery_link.png)
_[그림7] GA4 BigQuery 링크 연결_

이후 절차는 화면을 캡처해두지 않아서 글로 대체합니다...

1. BigQuery 프로젝트 선택
    - 저장할 GCP 프로젝트 선택합니다.
    - 참고로 하나의 GA4 속성에는 하나의 BigQuery 프로젝트만 연결할 수 있습니다.
    - 데이터 저장 위치를 선택합니다. 저의 경우 지리적으로 가장 가까운 asia-northeast3(서울 지역)로 설정했습니다.
2. 설정 구성
    - "데이터 스트림 및 이벤트"와 "빈도"를 설정할 수 있습니다.
    - 여기서 이전에 생성한 데이터 스트림을 설정합니다!
    - 빈도는 매일(batch) 또는 스트리밍(stream) 방식을 지원합니다.
    - 스트리밍 방식은 1GB당 $0.05의 추가 비용이 발생합니다.

![BigQuery 링크의 데이터 스트림 설정](/assets/img/data/5007/5007_08_ga4_bigquery_datastream.png)
_[그림8] BigQuery 링크의 데이터 스트림 설정_

3. 검토 후 제출
    - 마지막으로 설정 내용을 검토한 뒤 제출을 클릭합니다.


### 3. BigQuery 데이터 세트 확인

설정이 완료되면 BiqQuery에서 새로운 데이터 세트와 테이블을 확인할 수 있습니다.

![GA4와 연결된 BigQuery 데이터 세트](/assets/img/data/5007/5007_09_bigquery_schema.png)
_[그림9] GA4와 연결된 BigQuery 데이터 세트_

### 4. 샘플 쿼리 실행

로우 데이터가 어느정도 쌓였다면 쿼리를 통해 데이터를 확인할 수 있습니다.

다음 쿼리는 쿼리 실행일로부터 90일 동안의 유입 경로를  user_pseudo_id를 기반으로 확인할 수 있는 쿼리입니다.

```sql
WITH TrafficAggTable AS (
  SELECT
    traffic_source.source AS traffic_source,
    COUNT(DISTINCT user_pseudo_id) AS acquired_users_count
  FROM
      `프로젝트이름.데이터세트이름.events_*`
  WHERE
      _TABLE_SUFFIX BETWEEN FORMAT_DATE("%Y%m%d", CURRENT_DATE() - INTERVAL 90 day) AND FORMAT_DATE("%Y%m%d", CURRENT_DATE())
  GROUP BY
      traffic_source
)
SELECT
  "total" AS traffic_source,
  SUM(acquired_users_count) AS acquired_users_count
FROM TrafficAggTable
UNION ALL
SELECT
    traffic_source,
    acquired_users_count
FROM
    TrafficAggTable
ORDER BY
    acquired_users_count DESC
;
```

쿼리 결과 34명의 유저를 확인할 수 있습니다.

아직 갈 길이 멀다는 걸 확인할 수 있군요 😅😅

![빅쿼리 쿼리 결과](/assets/img/data/5007/5007_10_bigquery_result.png)
_[그림10] BigQuery 쿼리 결과_

---
## 마무리하며

이번 글에서는 GA4와 BigQuery를 연동시키는 법을 알아보았습니다.

BigQuery에 로우 데이터를 쌓기 시작한지 1달 가량 되었습니다.

로우 데이터를 기반으로 어떤 Data Mart를 만들지, 이를 위해서는 어떤 ETL 과정이 필요할지 고민이 됩니다.

이 고민을 마무리한다면 시리즈를 계속 연재해보겠습니다.

이 글이 조금이나마 도움이 되셨으면 합니다.

감사합니다. 😀

---
## 참고 문헌

- [GA4] BigQuery Export, [https://support.google.com/analytics/answer/9358801?hl=ko](https://support.google.com/analytics/answer/9358801?hl=ko)
- 가격 책정_ BigQuery_ 클라우드 데이터 웨어하우스, [https://cloud.google.com/bigquery/pricing?hl=ko#data_ingestion_pricing](https://cloud.google.com/bigquery/pricing?hl=ko#data_ingestion_pricing)
- GA4 - BigQuery 연결_ GA4 데이터를 빅쿼리에 저장하기, [https://osoma.kr/blog/ga4-bigquery-connect/](https://osoma.kr/blog/ga4-bigquery-connect/)
