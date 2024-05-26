### quickSight

서버리스 머신 러닝 기반 비즈니스 인텔리전스 서비스이다.

→ 대시보드를 생성하고 소유한 데이터 소스와 연결할 수 있다.

+) 비즈니스 인텔리전스 = 대화형 대시보드 생성

- 시각화 가능, 빠르고 오토 스케일링이 가능하다. 웹사이트에 임베드할 수 있다. 세션당 비용을 지불
- 사용 사례

  : 비즈니스 분석, 시각화 구현, 임시 분석 수행, 비즈니스 인사이트 획득

- spice 엔진이라는 것이 있다.

= 인 메모리 연산 엔진이며 amazin quickSight로 데이터를 직접 가져올 때 사용된다.

다른 db에 연결되어 있을 때에는 작동하지 않는다.

- 훌륭한 사용자 수준의 기능을 제공한다.

→ 엔터프라이즈 에디션에는 액세스 권한이 없는 사용자에게 일부 열이 표시되지 않도록 표시하는 열 수준 보안을 설정할 수 있다.

### quickSight와 다른 서비스의 통합

aws의 다양한 데이터 소스와 통합할 수 있다. / 타사 데이터 소스와 통합할 수 있다. → 대신 quickSight가 지원하는 서비스형 소프트웨어여야 한다. / 타사 데이터베이스와 통합할 수 있다. / quickSight로 직접 엑셀 파일, csv 파일, json, tsv 문서 등 데이터 소스를 가져올 수 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/fcbba0be-c832-4470-bdfa-5d72fc99ce61/Untitled.png)

### quickSight - dashboard & 분석

- 스탠다드 버전: 사용자 정의
- 엔터프라이즈 버전: 그룹 정의
- 사용자와 그룹은 quickSight 서비스 전용이다.  IAM X
- 대시보드를 생성한다.

  → 읽기 전용 스냅샷으로 분석 결과를 공유할 수 있다.

  →  분석의 구성을 저장한다. (필터, 옵션 등이 저장된다.)

- 특정 사용자/그룹과 분석 결과나 대시보드를 공유할 수 있다.
- 액세스 권한이 있는 사용자는 기본 데이터를 볼 수 있다.

### aws glue

- 추출, 변환,로드 서비스를 관리한다. ETL(extract, transform, and load) service
- 분석을 위해 데이터를 준비하고 변환하는데 매우 유용하다.
- 완전 서버리스 서비스로 원하는 모든 작업을 수행할 것이다.

### glue - parquet 형식으로 변환

parquet은 열 기반의 데이터 형식이므로 athena 같은 서비스와 함께 사용하면 효과적이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/c8a32c9f-1996-4ba0-95e8-249d059f01f9/Untitled.png)

이 과정을 lambda를 이용해서 자동화할 수 있다.

### glue - 데이터 세트의 카탈로그

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/557ee74f-7a8f-45d9-8846-8ebc9feb5156/Untitled.png)

glue data catalog는 glue 데이터 크롤러를 실행해 amazon s3, rds, dynamodb, jdbc에 연결한다.

glue 데이터 크롤러는 데이터베이스를 크롤링하고 데이터베이스의 테이블 등의 모든 메타 데이터를 글루 데이터 카탈로그에 저장한다.

→ ETL을 수행하기 위한 글루 작업에 활용될 모든 데이터베이스, 테이블 메타데이터를 갖게된다.

amazon athena는 데이터와 스키마를 검색할 때 백그라운드에서 aws glue data catalog를 활용한다.

amazon redshift spectrum과 amazon emr도 마찬가지이다.

⇒ glue data catalog 서비스는 다른 여러 aws 서비스의 중심이다.

### glue 에 대해 전반적으로 알아야 할 기능

1. glue 작업(job) 북마크: 새 ETL 작업을 실행할 때 이전 데이터의 재처리를 방지한다.
2. glue elastic views
    1. sql을 사용해 여러 데이터 스토어의 데이터를 결합하고 복제한다.
    2. 커스텀 코드를 지원하지 않으며 glue가 원본 데이터의 변경 사항을 모니터링한다. 서버리스이다.
    3. 여러 데이터 스토어에 분산된 구체화된 뷰인 가상 테이블을 생성할 수 있다.
3. glue databrew: 사전 빌드된 변환을 통해 데이터를 정리하고 정규화한다.
4. glue studio: glue에서 ETL 작업을 생성,실행, 모니터링하는 gui이다.
5. glue streaming ETL: apache spark structured streaming 위에 빌드되며 ETL 작업을 배치 작업이 아니라 스트리밍 작업으로 실행할 수 있다. kinesis data streaming kafka/msk에서 glue 스트리밍 ETL을 사용해 데이터를 읽을 수 있다.

### lake formation

데이터 레이크 생성을 돕는다

데이터 레이크 = 데이터 분석을 위해 모든 데이터를 한곳으로 모아주는 중앙 집중식 저장소이다.

→ 레이크 포메이션: 데이터 레이크 생성을 수월하게 해주는 완전 관리형 서비스이다.

lake formation은 데이터 레이크에서의 데이터 검색, 정제, 변환 주입을 돕는다.

데이터 수집, 정제, 카탈로깅, 복제같은 복잡한 수작업을 자동화하고 기계학습 변환 기능으로 중복 제거를수행한다.

data lake에서는 정형 데이터와 비정형 데이터 소스를 결합할 수 있다.

블루 프린트를 제공한다.

+) 내장된 블루 프린트는 데이터를 데이터 레이크로 이전하는 것을 도와주며 s3, rds, RDB, 온프레미스데이터베이스 등에서 지원된다.

애플리케이션에서 행, 열 수준의 세분화된 액세스 제어를 할 수 있다.

⇒ lake formation에 연결된 애플리케이션에서는 세분화된 액세스 제어가 가능하다.

aws 글루 위에 빌드된다. 하지만 glue와 직접 상호작용하지는 않는다.

### aws lake formation

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/20f597aa-a403-4e40-9610-5c0b58715e67/Untitled.png)

s3에 저장되는 데이터 레이크의 생성을 돕는다.

데이터 소스로는 s3, rds, aurora, 온프레미스 데이터베이스가 있다.

lake formation의 블루 프린트를 통해 데이터를 주입한다.

lake formation에는 소스 크롤러와 etl 및 데이터 준비 도구, 데이터 카탈로깅 도구가 포함된다

→ glue의 기본서비스에 해당한다.

데이터 레이크의 데이터를 보호하는 보안설정과 액세스 제어에도 포함된다.

lake formation을 활용하는 서비스로는 athena, redshift, emr, spark 분석 도구가 있다.

사용자는 이런 서비스를 통해 lake formation 및 데이터 레이크에 연결한다.

### lake formation을 왜 사용할까?

**중앙화된 권한 때문에!!!**

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/466d5003-69f4-4011-8c4f-7ce698234a76/Untitled.png)

회사가 데이터 분석에 athena, quickSight를 사용할 때 사용자는 허용된 데이터만 볼 수 있어야 하고 읽기 권한이 있어야 한다.

데이터 소스는 s3, rds, aurora가 있다.

athena에 보안을 설정하거나 quickSight에서 사용자 수준의 보안을 설정할 수 있다.

아니면 s3 버킷 정책이나 사용자 정책에 보안을 설정할 수 있다.(rds, aurora도 마찬가지로)

→ 보안을 관리할 곳이 많아지면 엉망이 된다!

⇒ 액세스 제어 기능과 열 및 행 수준의 보안이 있는 lake formation을 이용하자

lake formation에 주입된 모든 데이터는 중앙 s3 버킷에 저장되지만 모든 액세스제어와 행/열 수준 보안은 lake formation 내에서 관리된다.

→ lake formation에 연결하는 서비스는 읽기 권한이 있는 데이터만 볼 수 있다.

### kinesis data analytics

두 종류가 있다.

1. sql 애플리케이션용
2. apache flink용

### kinesis data analytics for SQL applications

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/a702400a-7412-4afa-aea3-a0f400c29d2a/Untitled.png)

중앙에 위치하며 kinesis data stream, kinesis data firehose 데이터 소스에서 데이터를 읽는다.

이 곳에서 읽어와서 sql문에 적용하여 실시간 분석을 처리할 수 있다.

s3 버킷의 데이터를 참조하여 참조 데이터를 조인할 수 있다.

→ 실시간 데이터가 풍성해진다.

그리고 여러 대상에 데이터를 전송한다.

- 전송 방법
1. kinesis data streams

   : kinesis data analytics의 실시간 쿼리로 스트림을 생성한다.

→ ec2에서 실행하는 애플리케이션, lambda로 스트릉밍하는 데이터를 실시간으로 처리할 수 있다.

1. kinesis data firehose

   : kinesis data firehose로 바로 전송한다.

→ s3, redshift, opensearch, 기타 firehose 대상에 전송된다.

- **세부 내용**
1. 데이터 소스로는 kinesis data streams & kinesis data firehose가 있다.
2. s3로 데이터를 강화할 수 있다.
3. 완전 관리형에 프로비저닝할 필요가 없다.
4. 오토 스케일링이 가능하다.
5. 실제 전송된 비용 만큼만 계산된다.
6. 결과:
    1. kinesis data streams/kinesis data firehose에 데이터를 출력할 수 있다.
7. 사용 사례: 시계열 분석, 실시간 대시보드, 실시간 지표

### kinesis data anayltics for Apache Flink

- apache flink를 사용하면 java, scala, sql로 애플리케이션을 작성하고 스트리밍 데이터를 처리/분석할 수 있다.

  flink는 코드로 작성해야 하는 특별한 애플리케이션이다.

  → 이 애플리케이션을 kinesis data analytics의 flink 전용 클러스트에서 실행할 수 있다.

  → apache flink를 사용하여 두 개의 메인 데이터 소스인 kinesis data streams/ amazon MSK의 데이터를 읽을 수 있다.

- aws의 관리형 클러스터에서 apache flink 애플리케이션을 실행할 수 있다.

  → apache flink는 표준 sql보다 훨씬 강력하기 때문에 고급 쿼리 능력이 필요하거나 kinesis data streams나 aws의 관리형 kafka인 msk같은 서비스로부터 스트리밍 데이터 읽을 능력이 필요할 때 사용한다.

    - 컴퓨터 리소스를 자동 프로비저닝할 수 있고 병렬 연산과 오토 스케일링을 할 수 있다.
    - 체크포인트와 스냅샷으로 구현되는 애플리케이션 백업이 가능하다.
    - apache flink 프로그래밍 기능을 사용할 수 있다.
    - flinkt는 kinesis data firehose의 데이터는 읽지 못한다.

      → 하고싶다면 sql 애플리케이션용 kinesis data analytics를 사용해야 한다.


### amazon managed streaming for apache kafka (amazon msk)

- kafka는 amazon kinesis의 대안입니다.
- aws의 완전 관리형 kafka 클러스터 서비스이다.
    - 그때그때 클러스터를 생성/업데이트/삭제한다.
    - msk는 클러스터 내 브로커 노드와 zookeeper 브로커 노드를 생성 및 관리한다.
    - 고가용성을 위해 vpc의 클러스터를 최대 3개의 다중 az 전역에 배포한다.
    - 일반 kafka 장애를 자동 복구하는 기능이 있다.
    - EBS 볼륨에 데이터를 저장할 수 있다.
- amazon msk 서버리스를 사용할 수 있다.
    - msk에서 apache kafka를 실행하지만 서버 프로비저닝이나 용량 관리가 필요 없다.
    - msk가 리소스를 자동으로 프로비저닝하고 컴퓨팅과 스토리지를 스케일링한다.

### apache kafka 살펴보기

apache kafka는 데이터를 스트리밍하는 방식이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/6cb04a46-e19e-4db9-be7d-0157fee36d00/Untitled.png)

kafka 클러스터는 여러 브로커로 구성되고 데이터를 생산하는 생산자는 데이터를 클러스터에 주입한다.

생산자가 kafka 주제로 데이터를 전송하면 해당 데이터는 다른 브로커로 완전 복제된다.

ㅍㅍ 주제는 실시간으로 데이터를 스트리밍하고 소비자는 데이터를 소비하기 위해 주제를 폴링한다.

소비자는 데이텨를 원하는 대로 처리하거나 다른 대상으로 보낸다.

### kinesis data streams vs amazon MSK

- kinesis data stream
1. 1mb의 메시지 크기 제한이 있다.
2. 샤드로 데이터를 스트리밍한다.
3. 용량을 확장하려면 샤드 분할을, 축소하려면 샤드 병합을 한다.
4. TLS 전송 중 암호화 기능이 있다.
5. 저장 데이터 암호화가 가능하다.

- amazon MSK
1. 1mb이 기본값이고 더 큰 메시지 보존을 위해 10mb로 설정할 수 있다.
2. 파티션을 이용한 kafka 주제를 사용한다.
3. 파티션 추가로 주제 확장만 할 수 있다.
4. 평문과 TLS 전송 중 암호화 기능이 있다.
5. 저장 데이터 암호화가 가능하다.
6. 원한다면 1년 이상 데이터를 보관할 수 있다.

### amazon MSK 소비자

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/844ffa7b-a965-437f-9503-473d356da9e2/Untitled.png)

msk의 데이터를 소비하는 방법은 여러 가지가 있다.

### 빅데이터 수집 파이프라인

이상적으로 애플리케이션 수집 파이프라인이 완전 서버리스이고 aws로 완전 관리되기를 바란다.

또 데이터를 실시간으로 수집하고 싶다.

변환도 하고싶고 변환된 데이터를 sql을 이용해서 쿼리하고 싶어한다.

그리고 이 쿼리를 이용해서 만드는 보고서는 s3에 저장하고 싶고 데이터를 대시보드에 로그하고 싶다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/5bda1725-c453-4666-916c-2923277c12e6/Untitled.png)

1. 데이터의 생산자가 IoT 기기라고 가정해보자

+) IoT core는 이 기기를 관리할 수 있도록 하는 서비스이다.

1. 이 기기들은 실시간으로 IoT 코어에 데이터를 전송할 수 있고 IoT 코어는 바로 kinesis data stream으로 전송한다.

   = kinesis 서비스로 매우 빠르게 빅데이터를 실시간으로 파이프할 수 있도록 해준다.

2.
    1. kinesis는 kinesis data firehose와 소통한다.

       firehose는 예를 들어 매분마다 s3 버킷으로 데이터를 넣거나 오프로드 할 수 있도록 해준다.

       ⇒ 수집 버킷

    2. lambda를 통해 데이터를 정리하거나 매우 빠르게 변환하는 작업을 할 수 있다.
3. 수집 버킷이 있어 SQS 큐를 작동시킬 수 있다.
4. SQS 큐가 lambda 함수를 작동할 수 있다.
5. lambda는 amazon athena sql 쿼리를 작동시킨다. 이 쿼리가 수집 버킷에서 데이터를 꺼내고 서버리스인 sql 쿼리를 수행할 것이다.
6. 이 서버리스 쿼리의 출력은 보고 버킷으로 들어간다.
7.
    1. quicksight를 이용해서 데이터를 시각화할 수 있다.
    2. redshift를 이용해서 데이터 분석을 할 수 있다.