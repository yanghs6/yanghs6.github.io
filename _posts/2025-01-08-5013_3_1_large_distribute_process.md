---
date: 2025-01-08 19:35:00 +/-0900
title: "[Data] [빅데이터를 지탱하는 기술] 정리하기 - 3-1 대규모 분산 처리의 프레임워크"
categories: [Data, concept]
tags: [데이터(data), 개념(concept), 분산처리(distributed_processing), 비정규화(denormalization), 쿼리엔진(query_engine), hadoop, hdfs, hive, mapreduce, spark]
description: 빅데이터를 지탱하는 기술 시리즈로 3장 1절, 대규모 분산 처리의 프레임워크를 정리했습니다.

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

이번 글에서는 "빅데이터를 지탱하는 기술" 이라는 책의 3장 1절, "대규모 분산 처리의 프레임워크"를 정리했습니다.

---
## 구조화 데이터와 비구조화 데이터

구조화된 데이터(Structured Data)는 스키마가 명확하게 정의된 데이터를 뜻합니다. 스키마(Schema)는 테이블 칼럼 명이나 데이터의 형태 그리고 테이블 간의 관계 등을 모두 포함합니다.

반대로 비구조화 데이터(Unstructured Data)는 스키마가 없는 데이터로 자연 언어의 텍스트나 이미지, 동영상 같은 미디어 데이터 또는 SQL로 집계할 수 없는 데이터를 의미합니다.

![[그림 3-1] 구조화 데이터, 비구조화 데이터, 스키마리스(반구조화) 데이터](/assets/img/data/5013/5013_01_structured_data_unstructured_data_schemaless_data.png)
_[그림 3-1] 구조화 데이터, 비구조화 데이터, 스키마리스(반구조화) 데이터_

데이터 레이크는 비구조화 데이터를 분산 스토리지에 저장하고, 이를 분산 시스템에서 처리합니다.

### 스키마리스 데이터 - 기본 서식을 있지만, 스키마가 정의 안 됨

스키마리스 데이터(Schemaless Data, Semi-Structured Data)는 서식은 있으나 컬럼의 수나 데이터 형이 명확하지 않은 데이터입니다. 대표적으로 CSV나 JSON, XML 등이 있으며 몇몇 NoSQL 데이터베이스는 스키마리스 데이터에 대응합니다.

인터넷을 통해 주고받는 데이터는 JSON 비율이 높습니다.

### 데이터 구조화의 파이프라인 - 테이블 형식으로 열 지향 스토리지에 장기 보존

![[그림 3-2] 데이터 구조화의 파이프라인](/assets/img/data/5013/5013_02_pipeline_of_data_structure.png)
_[그림 3-2] 데이터 구조화의 파이프라인_

위 그림은 데이터 파이프라인의 예시를 보여줍니다.

각 데이터 소스로부터 비구조화 데이터와 스키마리스 데이터를 분산 스토리지에 저장합니다. 분산 스토리지의 데이터는 명확한 스키마가 없는 경우가 많으므로 이를 스키마가 명확한 구조화 데이터로 변환해야 합니다.

이후 데이터 압축률을 위해 구조화 데이터를 열 지향 스토리지에 저장합니다. 구조화 데이터 중 시간에 따라 증가하는 데이터는 팩트 테이블, 그에 따른 부속 데이터는 디멘전 테이블로 취급합니다.

### 열 지향 스토리지의 작성 - 분산 스토리지 상에 작성해 효율적으로 데이터 집계

열 지향 스토리지는 크게 MPP 데이터베이스와 Hadoop으로 분류할 수 있습니다. MPP 데이터베이스는 제품에 따라 스토리지 형식을 고정할 수 있으며 사용자가 상세사항을 몰라도 동작합니다. Hadoop은 사용자가 직접 열 지향 스토리지의 형식을 선택하며 선호하는 쿼리 엔진에서 집계합니다.

Hadoop에서의 열 지향 스토리지도 구분할 수 있는데요. Apache ORC는 구조화된 데이터용 열지향 스토리지로 처음에 스키마의 초기화가 필요합니다. Apache Parquet은 스키마리스에 가까운 데이터를 저장하며 JSON 같은 데이터도 그대로 저장할 수 있습니다. 이 책에서는 Apache ORC에 의한 스토리지 형식을 채택했네요!

## Hadoop - 분산 데이터 처리의 공통 플랫폼

들어가기 앞서 Hadoop의 간단한 역사를 살펴보겠습니다. 2003년에 오픈 소스 웹 크롤러인 Nutch의 분산 파일 시스템으로 시작한 Hadoop은 2006년 단일 프로젝트로 독립하고 Apach Hadoop이 배포됩니다.

이후의 역사는 다음의 표와 같습니다.

- [표 3-1] Hadoop과 그 주변 프로젝트의 역사(~2016년)

    | 시기   | 이벤트                              |
    | ------ | ----------------------------------- |
    | 2003년 | Nutch 프로젝트 발족                 |
    | 2004년 | Google MapReduce 논문               |
    | 2006년 | Apache Hadoop 프로젝트 발족         |
    | 2011년 | Apache Hadoop 1.0.0 배포            |
    | 2013년 | Apache Hadoop 2.2.0 배포(YARN 대응) |
    | 2014년 | Apache Spark 1.0.0 배포             |
    | 2016년 | Apache Flink 1.0.0 배포             |
    | 2016년 | Apache Mesos 1.0.0 배포             |

다음 그림은 빅데이터와 관련된 Apache 프로젝트 중 일부로 각 계층에 대해 천천히 살펴보겠습니다.

![[그림 3-3] 빅데이터 관련 Apache 프로젝트(일부)](/assets/img/data/5013/5013_03_apache_projects_related_to_big_data_partial.png)
_[그림 3-3] 빅데이터 관련 Apache 프로젝트(일부)_

### 분산 시스템의 구성 요소 - HDFS, YARN, MapReduce

Hauoop의 기본 구성 요소는 분산 파일 시스템(Distributed File System)인 **<u>HDFS(Hadoop Distributed File System)</u>**,리소스를 관리하는 **<u>YARN(Yet Another Resource Negotiator)</u>**, 분산 데이터를 처리하는 **<u>MapReduce</u>**입니다.

이 외의 프로젝트는 Hadoop 본체와 독립적으로 개발되어 Hadoop을 이용한 분산 애플리케이션으로 동작합니다. 예를 들어 분산 파일 시스템은 HDFS을 사용하지만 리소스 관리에는 Mesos, 분산 데이터 처리에는 Spark를 채택할 수 있습니다. 따라서 각자의 환경에 알맞은 소프트웨어를 선택 후 조합할 수 있다는 장점을 가집니다.

### 분산 파일 시스템과 리소스 관리자 - HDFS, YARN

![[그림 3-4] Hadoop에 의한 리소스 관리](/assets/img/data/5013/5013_04_resource_manage_by_hadoop.png)
_[그림 3-4] Hadoop에 의한 리소스 관리_

위의 그림은 HDFS와 YARN으로 구성된 간단한 Hadoop 구성을 나타냅니다. Hadoop에서 처리되는 데이터의 대부분은 분산 파일 시스템인 HDFS에 저장합니다. 이는 네트워크에 연결된 파일 서버와 유사한데요. 간단히 소개하자면 실제 데이터를 블록 형태로 추상화해 다수의 데이터 노드에 저장하고 클라이언트 - 데이터 노드 간의 통신을 네임 노드로 제어합니다. 이를 통해 데이터의 유실 방지나 복구, 대량 데이터릐 효율적인 처리 등이 가능합니다.

이에 대한 계산의 리소스는 리소스 관리자인 YARN이 담당합니다. 애플리케이션이 사용하는 CPU 코어와 메모리를 컨테이너(Container) 단위로 관리하는데요. Hadoop에서 분산 애플리케이션을 실행한다면 YARN이 클러스터 전체의 부하를 확인한 뒤 비어있는 호스트로부터 컨테이너를 할당합니다.

분산 시스템의 리소스는 호스트 수에 따라 그 상한을 결정합니다. 한정된 리소스로 다수의 분산 애플리케이션을 동시에 실행한다면 리소스 쟁탈이 발생하는데 이 때 리소스 관리자가 위의 상활을 제어합니다. 애플리케이션마다 우선순위를 부여하며 배치 처리에는 비교적 낮은 우선순위를 부여하는 방식이 대표적입니다.

### 분산 데이터 처리 및 쿼리 엔진 - MapReduce, Hive

![[그림 3-5] Hive on MR의 실행 과정](/assets/img/data/5013/5013_05_execution_process_of_hive_on_mr.png)
_[그림 3-5] Hive on MR의 실행 과정_

MapReduce는 YARN에서 동작하는 분산 애플리케이션입니다. 분산 시스템에서 데이터를 처리하며 임의의 자바 프로그램을 실행할 수 있어 비구조화 데이터 가공에 적합합니다. MapReduce는 크게 작업을 나눠 처리하는 Map과 처리된 결과를 집계하는 Reduce로 나뉩니다.

대량의 배치 처리를 위한 시스템으로 이러한 데이터를 빠르게 처리할 수 있지만, 오버헤드가 크기 때문에 수 초의 짧은 쿼리에는 부적합합니다. Hive도 배치 처리에 적합하지만 역시 애드 혹 쿼리를 반복적인 실행에는 부적합합니다.

Apache Hive는 SQL 등 쿼리 언어를 통해 데이터를 집계하고 이를 자동으로 MapReduce합니다. 참고로 MapReduce에 대해서는 추후 자세히 다뤄보겠습니다.

### Hive on Tez

![[그림 3-6] Hive on Tez의 실행 과정](/assets/img/data/5013/5013_06_execution_process_of_hive_on_tez.png)
_[그림 3-6] Hive on Tez의 실행 과정_

Apache Tez는 기존의 MapReduce를 대체하는 목적으로 만들어졌습니다. MapReduce 프로그램에서는 1회의 MapReduce 스테이지가 끝날 때까지 다음 작업을 기다리지만 Tez는 스테이지 종료를 기다리지 않고 차례대로 후속 처리에 전달합니다. 현재의 Hive는 Tez를 사용해도 동작하도록 작성되어 Hive on Tez라고도 불립니다.

### 대화형 쿼리 엔진 - Impala 와 Presto

![[그림 3-7] Presto와 Impala의 실행 과정](/assets/img/data/5013/5013_07_execution_process_of_presto_and_impala.png)
_[그림 3-7] Presto와 Impala의 실행 과정_

대화형 쿼리 실행을 위한 쿼리 엔진으로는 Impala와 Presto가 있습니다. 순간 최대 속도를 높이기 위해 모든 오버헤드를 제거하고 리소스를 최대로 활용해 쿼리를 실행하기 때문에 MPP 데이터베이스 수준의 응답 시간을 보여줍니다.

Hadoop에서는 성질이 다른 쿼리 엔진을 목적에 따라 구분하는데요. Hive를 대량의 비구조화 데이터를 가공하는 무거운 배치를 처리한다면 Impala와 Presto는 완성한 구조화 데이터를 대화식으로 집계합니다.

Hadoop에서는 다수의 쿼리 엔진이 개발되어 있는데 이를 SQL-on-Hadoop이라고 부릅니다. 이들은 분산 스토리지에 저장된 데이터를 신속하게 집계할 수 있습니다!

## Spark - 인 메모리 형의 고속 데이터 처리

Spark는 대량의 메모리를 활용하여 고속화를 실현했습니다. MapReduce는 데이터 처리 과정에서 만들어진 중간 데이터를 디스크에 기록하는데요. Spark는 가능한 많은 데이터를 메모리상에 올리고 디스크에 기록하지 않습니다.

### MapReduce 대체하기 - Spark의 입지

![[그림 3-8] Spark의 실행 과정](/assets/img/data/5013/5013_08_execution_process_of_spark.png)
_[그림 3-8] Spark의 실행 과정_

이 Spark는 MapReduce를 대체하고 있습니다. 분산 파일 시스템(HDFS)나 리소스 관리자(YARN)에 대응하며 Hadoop 없이 Amazon S3나 카산드라(Cassandra)를 이용합니다.

Spark의 실행에는 자바 런타임이 필요하지만 Spark 상 데이터 처리는 스크립트 언어로도 사용할 수 있습니다. 현재 채택한 표준은 자바, 스칼라, 파이썬, R입니다. 또한 단순한 MapReduce의 역할 외에도  SQL의 쿼리를 실행하는 Spark SQL이나 스트림을 처리하는 Spark Straming 등 다양한 기능을 지원합니다.

---
## 마무리하며

이번 글에서는 대용량 분산 처리 과정에 대해 알아봤습니다.

들어가기 앞서 데이터를 정해진 형식의 유무에 따라 구조화, 비구조화, 스키마리스로 구분짓습니다.

이후 이러한 데이터를 처리하기 위한 대표적인 분산 처리 플랫폼 Hadoop를 소개합니다. Hadoop은 데이터를 저장하는 분산 파일 시스템과 이를 처리하는 MapReduce, 그리고 여러 자원을 관리하는 리소스 관리자로 구성됩니다.

특히 MapReduce와 쿼리 엔진에 대해서는 구체적으로 다루는데요. 분산 데이터 처리의 Tez나 Spark, 쿼리 엔진인 Impala와 Presto 등의 기술을 살펴보면서 각각의 세부적인 차이를 확인합니다.

YARN을 제외한 기술은 실제로 경험한 적이 없어 보다 재밌게 공부할 수 있었습니다!

이 글이 조금이나마 도움이 되셨으면 합니다.

감사합니다. 😀

---
## 참고 문헌

- 니시다 케이스케, *빅데이터를 지탱하는 기술*, 제이펍, 2018