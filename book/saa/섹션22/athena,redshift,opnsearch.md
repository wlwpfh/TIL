## athena

amazon s3 버킷에 저장된 데이터 분석에 사용하는 서버리스 쿼리 서비스이다.

→ 데이터를 분석하려면 표준 sql 언어로 파일을 쿼리해야 한다.

+) athena는 sql 언어를 사용하는 presto 엔진에 빌드된다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/39e05318-d9f3-4a62-8f69-79ed4b4cdd71/Untitled.png)

- csv, json, orc, avro parquet 등 다양한 형식을 지원한다.
- 가격 정책: 스캔된 데이터의 tb 당 고정 가격
- 전체 서비스가 서버리스이기에 데이터베이스를 프로비저닝할 필요가 없다.
- amazon quickSight라는 도구와 함께 사용하는 일이 많다.

→ 보고서와 대시보드 생성

사용사례: 임시 쿼리 수행, 비지니스 인털리전스 분석 및 기록, 모든 aws 서비스의 로그를 쿼리하고 분석할 수 있다.

### athena - 성능 향상

- 비용을 지불할 때 스캔된 데이터의 tb당 가격을 지불하므로 데이터를 적게 스캔할 유영의 데이터를 사용한다.

→ 열 기반(columnar) 데이터 유형을 사용하면 필요한 열만 스캔할 수 있다

→ apache parquet과 orc를 추천한다.

→ 성능이 크게 향상된다.

→ 파일을 apache parquet나 orc 형식으로 가져오려면 glue를 사용하면 된다.

- 더 적은 데이터를 스캔해야 하므로 데이터를 압축해 더 적게 검색해야 한다.
- 특정 열을 항상 쿼리한다면 데이터 세트를 분할하면 된다.

  → s3에 있는 데이터를 정리하고 분할하는 것

- 큰 파일을 사용해서 오버헤드를 최소화하면 성능을 향상시킬 수 있다.

ex) 128mb보다 큰

### athena -  연합 쿼리

- athena는 s3 뿐만 아니라 어떤 곳의 데이터도 쿼리할 수 있다.

→ 객체, 사용자 지정 데이터 원본 등

- 데이터 원복 커넥터를 사용하여 aws나 온프레미스에서 사용 가능하다.

→ 데이터 원복 커넥터는 람다 함수로 다른 서비스에서 연합 쿼리를 실행한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/d007d41f-bec4-4281-88c4-5555ec2d2c05/Untitled.png)

- 쿼리 결과는 사후 분석을 위해 s3 버킷에 저장할 수 있다.

---

## redshift

= 데이터베이스 + 분석 엔진

- postgresql에 기반하지만 온라인 거래 처리(OLTP)에는 사용되지 않는다.
- OLAP(온라인 분석 처리) 유형의 데이터베이스이다.

→ 분석과 데이터 웨어하우징에 사용된다.

- 다른 모든 데이터 웨어하우스보다 10배 좋은 성능을 가지고 있으며 pb의 데이터로 확대 가능하다.
- 데이터를 모두 redshift로 로드하면 순식간에 그 안에서 무작위화 할 수 있다.
- 성능 개선에 좋다.

→ 데이터를 열 기반 스토리지로 사용하기 때문에

→ 행 기반과 달리 병렬 쿼리 엔진이 있는 것이다.

- redshift 클러스터에 공급한 인스턴스 만큼 비용을 지불해야 한다.
- 쿼리를 수행하기 위해서는 sql 문장을 바로 사용하면 된다.
- amazon quickSights/tableau 같은 비즈니스 인텔리전스 툴이 모두 통합된다.

→ redshift와 athena를 비교하자만 redshift는 다 로드해야 하지만 쿼리는 더 빠르고 빠른 조인을 할 수 있다.

⇒ 인덱스가 있어 더 빠르고 고성능 데이터 웨어하우스를 위해 인덱스를 빌드한다.

### redshift cluster

1. 리더 노드

   : 쿼리 계획과 결과 집합을 시킨다.

1. 계산 노드

   : 쿼리를 수행하고 결과를 리더에게 전송한다.

⇒ 둘 다 redshift cluster이기 때문에 노드 사이즈를 사전에 공급해야 한다.

→ 비용을 줄이고 싶다면 예약 인스턴스를 사용할 수 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/576fc2dd-ac87-4c30-ba02-0d7453794c4b/Untitled.png)

### redshift - snapshots & dr

redshift는 대부분 클러스터에 대해 싱글 AZ이지만 특정 클러스터 유형에 대해 멀티 AZ 모드가 있다.

- 스냅샷은 클러스터를 위한 point-in-time 백업이며 s3에 내부적으로 저장되고 증가한다.

→ 변화가 생긴 것만 저장될 것 → 많은 저장 공간이 절약된다.

- redshift 클러스터로 스냅샷을 복구할 수 있다.
- 스냅샷의 두 모드
    1. 수동 - 우리가 삭제할 때까지 저장되어 있다.
    2. 자동 - 매 8시간/5gb마다 스냅샷이 찍힌다. 자동화된 스냅샷을 위한 저장 기간을 설정할 수 있다.
- **redshift가 스냅샷을 자동으로 복사하도록 구성할 수 있다.**

  자동, 수동의 여부와는 관계 없이, 해당 클러스터에서 다른 aws 리전으로

  → 피해 복구 전략 제공


![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/28dbe092-856d-4f5e-96e7-d79bc277d798/Untitled.png)

### redshift로 데이터를 어떻게 수집하는 지

세 방법이 있다.

1. amazon kinesis data firehose
2. s3 using copy command
3. ec2 instance using jdbc driver

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/91074b09-20a1-4cc9-b367-5abf1ce9b3c7/Untitled.png)

### redshift spectrum

s3에 데이터가 있고 redshift를 이용해서 분석하고 싶지만 redshift로 로드하고 싶지 않을 때 사용할 수 있다.

+) 더 많은 처리 능력을 사용하고 싶을 때 사용할 수 있다.

**이를 사용하려면 쿼리를 시작할 수 있는 redshift 클러스터가 이미 있어야 한다.**

쿼리가 시작하면 해당 쿼리가 s3에 있는 우리의 데이터로 쿼릴르 수행할 수천개의 redshift spectrum 노드로 제출될 것이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/4e3b17fd-fde4-4082-b49a-7156b881fd9e/Untitled.png)

---

## amazon openSearch Service

amazon elasticSearch의 후속작이다.

openSearch를 이용하면 모든 필드를 검색할 수 있다. + 부분 매칭도 가능

→ 애플리케이션에 검색 기능을 제공하는 방식으로 널리 사용

+) 분석적 쿼리에도 사용 가능하다.

- openSearch 클러스터를 프로비저닝하는 두 모드가 있다.
    1. 관리형 클러스터 모드: 실제 물리적인 인스턴스가 프로비저닝 된다.
    2. 서버리스 클러스터: 서비스 경로를 선택, 스케일링부터 운영까지 모두 aws에서 관리하게 된다.
- 자체 쿼리 언어가 있다.

  → 자체적으로는 sql을 지원하지 않지만 플러그인을 통해 sql 호환성을 활성화할 수 있다.


ex) kinesis data firehose, aws IoT, cloudwatch logs, 커스텀 빌드 앱 등 다양한 곳에서 오는 데이터를 받을 수 있다.

- 보안은 cognito, iam 등을 통해 제공되나.
- 주소 암호화와 전송중 암호화를 사용할 수 있다.
- opensearch 대시보드라는 걸 이용해서 openSearch 데이터를 시각화할 수 있다.

### openSearch의 흔한 사용 패턴 - dynamoDB

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/00f5f59d-bfd0-4f25-9442-5d790e3ac18d/Untitled.png)

1. dynamoDB에 데이터가 담긴다.
2. 모든 스트림을 dynamoDB stream에 보낸다.
3. 람다 함수가 그 스트림을 잡는다.
4. 잡아서 실시간으로 amazon opensearch에 데이터를 넣는다.
5. 이 과정을 통해 애플리케이션은 특정 항목을 검색할 능력을 갖게 된다.
6. 아이템을 얻으면 dynamoDB를 호출해서 실제 그 항목을 테이블에서 받게 된다.

### openSearch의 흔한 사용 패턴 - cloudWatch Logs

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/9fbf621b-ece0-4e32-9226-941f29c7b187/Untitled.png)

1. cloudwatch logs subscription filter를 사용해서 데이터를 실시간으로 aws가 관리하는 람다 함수에 전송한다.

2-a. 람다 함수가 모든 데이터를 실시간으로 opensearch로 전송한다.

2-b. kinesis data firehose가 subscription filter로부터 읽을 수 있고 이어서 data firehose이기 때문에 근 실시간으로 데이터가 opensearch로 전송된다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/52d1d287-1559-475d-adf7-7d4be25c2f99/Untitled.png)

---

## emr

= elastic mapreduce로 aws에서 빅데이터 작업을 위한 하둡 클러스터 생성에 사용한다.

→ 방대한 양의 데이터를 분석하고 처리할 수 있다.

- 하둡 클러스터는 프로비저닝해야 하며 수백개의 ec2 인스턴스로 구성될 수 있다.
- emr는 빅데이터 전문가가 사용하는 여러 도구와 함께 제공된다.

  → apache spark, hbase, presto flink, …

- emr는 위의 서비스에 대한 프로비저닝과 구성을 대신 처리해준다.
- 전체 클러스터를 자동으로 확장할 수 있고 스팟 인스턴스와 통합되어 가격 할인을 받을 수 있다.

### emr - 노드 타입 & 구매

emr는 ec2 인스턴스의 클러스터로 구성되며 여러 노드 타입이 있다.

1. 마스터 노드: 클러스터를 관리하고 다른 모든 노드의 상태를 조정하며 장기 실행해야 한다.
2. 코어 노드: 태스크를 실행하고 데이터를 저장한다. - 장기 실행
3. 태스크 노드(선택 사항): 테스트만 실행한다. - 주로 스팟 인스턴스를 사용한다.
- 구매 옵션
    - 온디맨드 ec2 인스턴스 유형을 사용하면: 신뢰할 수 있고 예측 가능한 유형의 워크로드를 얻게 된다. 절대 종료되지 않는다.
    - 예약 인스턴스를 사용하면: 최소 1년을 사용해야 하는 경우 비용을 크게 절약할 수 있다.

      가능한 경우에는 emr이 자동으로 예약 인스턴스를 사용한다.

      장기 실행해야 하는 마스터 노드와 코어 노드에 적합하다.

    - 스팟 인스턴스: 종료될 수 있으며 신뢰도는 떨어지지만 저렴하다.

      → 태스크 노드에 활용하는 것이 좋다.


emr에서 배포할 때는 장기 실행 클러스터에서 예약 인스턴스를 사용하거나 임시 클러스터를 사용해 특정 작업을 수행하고 분석 완료 후에 삭제할 수 있다.