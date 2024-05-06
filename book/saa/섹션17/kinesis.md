## Kinesis 개요

kinesis 를 활용하면 **실시간 스트리밍 데이터를 손쉽게 수집하고 처리하여 분석할 수 있다.**

+) 대규모 데이터 수집을 위한

- 구성
1. kinesis data stream: 데이터 스트림을 수집하고 처리하고 저장한다.
2. kinesis data firehose: 데이터 스트림을 aws 내부나 외부의 데이터 저장소로 읽어들인다.
3. kinesis data analytics: sql나 apache flink를 활용하여 데이터 스트림을 분석한다.
4. kinesis video stream: 비디오 스트림을 수집하고 처리하여 저장한다.

### kinesis data stream

시스템에서 큰 규모의 데이터 흐름을 다루는 서비스이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/e62859df-aeb0-4dc3-9138-6a0e36f164f2/Untitled.png)

여러 개의 샤드로 구성되어 있다.

샤드의 번호는 사전에 프로비저닝해야 한다. (사전에 몇 개의 샤드로 할 지 결정해야 함)

데이터는 모든 샤드에 분배된다.

샤드는 데이터 수집률이나 소비율 측면에서 스트림의 용량을 결정한다.

생성자는 kinesis data stream에 레코드를 전달한다.

- 레코드

= 파티션 키 + 데이터 블롭

파티션 키 - 레코드가 이용할 샤드를 결정하는 데 사용된다.

데이터 블롭 - 값 자체

생산자는 데이터 스트림으로 보낼 때 초당 1mb를 전송하거나 샤드당 1촣에 천개의 메시지를 전송할 수 있다.

이제 데이터가 스트림에 들어가면 많은 소비자가 이 데이터를 사용한다.

소비자가 레코드를 받으면 파티션 키, 샤드에서 레코드의 위치를 나타내는 시퀀스 번호, 데이터 자체를 의미하는 데이터 블롭이 있다.

소비 유형에는 초당 2mb의 처리량을 모든 소비자가 공유 / 소비자마다 샤드당 1초에 2mb씩 받기가 있다.

### kinesis data stream

1. 보존기간: 1 ~365일
2. 데이터를 다시 처리하거나 확인할 수 있다.
3. 데이터가 일단 kinesis로 들어오면 삭제할 수 없다. = 불변성
4. 데이터 스트림으로 메시지를 전송하면 파티션 키가 추가되고 파티션 키가 같은 메시지들은 같은 샤드로 들어가게 되며 키를 기반으로 데이터를 정렬할 수 있다.
5. 생산자: aws sdk, kinesis producer library, kinesis agent
6. 소비자:
    1.  직접 쓰는 방식의 kinesis client library, aws sdk
    2. 관리되는 aws lambda, kinesis data firehose, kinesis data analytics
7. 생산자와 소비자를 위해 커스텀 코드를 써야 한다.
8. 직접 scaling 관리 - shard splitting, merging
9. replay capability 지원

### kinesis data stream - 용량 유형

1. provisioned mode 전통적인 모드

프로비저닝할 샤드 수를 정하고 api를 활용하거나 수동으로 조정한다.

각 샤드는 초당 1mb나 천개의 레코드를 받아들인다.

출력량의 경우에는 각 샤드가 초당 2mb를 받아들인다.

샤드를 프로비저닝할 때마다 시간당 비용이 부과된다.

1. on-demand mode

프로비저닝을 하거나 용량을 관리할 필요가 없다.

시간에 따라 언제든 용량이 조정된다.

기본적으로 초당 4mb 또는 초당 4천개의 레코드를 처리한다.

→ 이 용량은 지난 30일동안 관측한 최대 처리량에 기반하여 자동으로 조정된다.

사전에 사용량을 예측할 수없는 경우에 사용한다.

### kinesis data stream security

IAM 정책을 사용하여 샤드를 생성하거나 샤드에서 읽어들이는 접근 권한을 제어할 수 있다.

HTTPS로 전송 중 데이터를 암호화할 수 있다.

미사용 데이터는 CMS로 암호화할 수 있다.

클라이언트 측에서 데이터를 암호화하거나 복호화할 수 있다.

⇒ 클라이언트측 암호화

kinesis에서 VPC 엔드포인트를 사용할 수 있다.

→ kinesis에 인터넷을 거치지 않고 프라이빗 서브넷의 인스턴스에서 직접 손쉽게 접근할 수 있다.

모든 api 요청은 cloudTrail로 감시할 수 있다.

### kinesis data firehose

소비자로부터 데이터를 가져올 수 있는 아주 유용한 서비스이다.

여기서 소비자는 applications, client, sdk, kinesis agent. kinesis data stream, amazon cloudwatch, aws ioT

이 애플리케이션들은 kinesis data firehose로 기록을 전송할 것이다.

그러면 kinesis data firehose는 데이터를 lambda 함수를 사용해 변환시킬지 선택할 수 있다.

데이터가 변환된 이후에는 대상에 일괄 처리되어 쓰이게 된다. (= batch write)

대상

1. aws 대상: s3, amazon redshift( 웨어하우징 데이터베이스로 s3로 데이터를 쓴 후에 복사 명령을 통해 복사된다.),  amazon opensearch
2. 3rd party 파트너 대상: datadog, splunk, …
3. custom 대상: HTTP 엔드포인트를 갖는 자신의 api가 있다면 대상으로 데이터를 보낼 수 있다.

이렇게 보낸 다음에는 모든/실패한 데이터를 s3 버킷으로 보낼 수 있다.

→ 완전 관리형 서비스로 자동 확장과 서버리스 기능을 가져 관리해야 할 서버가 없다.

→ firehose를 통과하는 데이터에만 비용을 지불하면 된다.

→ 거의 실시간이다.(배치로 묶어서 해서 약간의 지연이 있다.)

→ 여러 데이터 포맷, 변환, 압축을 지원한다.

→ 필요하다면 lambda를 이용해서 데이터의 변환을 쓸 수 있다.

→ 실패하거나 모든 데이터를 백업 s3로 보낼 수 있다.

→ 데이터 저장이 없다.

### Kinesis와 SQS FIFO에서 데이터의 정렬

파티션 키를 이용해서 kinesis로 데이터를 전송하자!

같은 파티션 키를 지정하면 해당 키는 언제나 동일한 샤드로 간다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/0bdb1c55-eadb-402b-8114-f10e3111603c/Untitled.png)

트럭과 각각의 샤드 사이가 직접적으로 연결된 것은 아니다.

kinesis가 파티션 키를 해시해서 어느 샤드로 보낼지 결정한다.

### SQS로 데이터 정렬

sQS 표준에는 순서가 없다.

SQS FIFO라는 선입선출방식이 있다.

→ 그룹 아이디를 사용하지 않으면 모든 메시지가 소비되는 방식은 보내진 순서에 따르며 소비자는 하나만 존재한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/51f2e732-b3fe-4e7d-b20f-25879712e583/Untitled.png)

### Kinesis vs SQS ordering

100개의 트럭, 5개의 샤드, 1개의 SQS FIFO 대기열

1. kinesis data stream

평균적으로 가지는 값은 샤드 당 트럭 20대이다.

트럭 데이터는 각 샤드에 순서대로 정렬된다.

동시에 가질 수 있는 최대 소비자 개수는 5개 뿐이다. (=샤드당 하나)

초당 최대 5mb의 데이터를 수신할 수 있다.

1. SQS FIFO

SQS FIFO 대기열은 하나다.

각 트럭 아이디에 상응하는 그룹 아이디 100개를 생성한다.

→ 소비자도 최대 100개가 될 수 있다.

초딩 300/ 배치를 사용하면 30000개의 메시지를 가진다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/1585b413-eaea-40bb-9269-b76a75705ea9/Untitled.png)

### Amazon MQ

rabitMQ와 activeMQ 두가지 기술을 위한 관리형 메시지 브로커 서비스이다.

amazon mq를 사용하면 해당 브로커의 관리형 버전을 클라우드에서 사용할 수 있다.

확장성이 크지 않다.

이것은 서버에서 실행된다.

고가용성을 위해 다중 az 설정을 실행할 수 있다.

amazon mq는 SQS처럼 보이는 대기열 기능과 SNS처럼 보이는 주제 기능을 단일 브로커의 일부로 제공한다.

### amazon MQ - high availability 고가용성