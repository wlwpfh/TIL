## SNS

메시지 하나를 여러 수신자에게 보낼 때 pub/sub라는 것을 사용할 수 있다.

중간에 SNS topic을 통해 다른 구독자로 메시지를 전달한다.

### Amazon SNS

이벤트 생산자는 하나의 SNS 주제에만 메시지를 보낸다.

이벤트 수신자/구독자는 해당 주제에 관련한 SNS 알림을 받으려는 사람이다.

⇒ SNS 주제 구독자는 해당 주제로 전송된 메시지를 모두 받게 된다.

현재 주제당 최대 1200만 이상의 구독자가 가능하다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/ed63c897-eb29-4aca-83a2-f2a333419682/Untitled.png)

### SNS integrates with a lot of AWS services

SNS는 다양한 서비스에서 데이터를 수신하기도 한다.

aws에서 알림이 발생하면 이러한 서비스가 지정된 SNS 주제로 알림을 보낸다.

### SNS 게시 방법

1. SDK 주제 게시를 사용해 SNS에 메시지를 게시한다.
    1. 토픽 만들기
    2. 하나 혹은 여러 구독 만들기
    3. SNS 주제에 게시
2. 직접 게시 (모바일 앱 SDK 전용)
    1. 플랫폼 애플리케이션 만들기
    2. 플랫폼 엔드포인트 만들기
    3. 플랫폼 엔드포인트에 게시

### SNS 보안

- 암호화

→ HTTPS API를 이용해서 비행 중 암호화를 한다.

→ KMS 키를 사용하여 미사용 암호화를 얻는다.

→ 원한다면 클라이언트 측 암호화를 할 수 있다.

- 액세스 제어

→ IAM 정책은 모든 SNS API에 대한 액세스를 규제할 수 있다.

- S3 버킷 정책과 유사한 SNS 액세스 정책이 있다.

### SNS , SQS : Fan out 패턴

만약 다수의 SQS 대기열로 메시지를 보내려 할 때, 모든 SQS 대기열에 개별적으로 전송하려고 하면 문제가 발생할 수 있다.

⇒ 그렇기에 fan out 패턴을 사용해야 한다.

→ SNS 토픽으로 한 번 메시지를 전송하고 원하는 만큼 SQS 대기열을 SNS 토픽에 구독시키는 것

이 대기열들은 구독자이고, SNS로 보내진 메시지들을 모두 받을 것이다.

이것은 와벽하게 분리된 모델로 데이터의 손실이 발생하지 않는다.

SQS → 데이터의 지속성, 지연 처리. 작업 재시도 등의 효과를 얻을 수 있다.

시간이 지날수록 SQS 대기열을 SNS 토픽의 구독자로 더 추가할 수 있다.

→ 이렇게 하기 위해서는 SQS 대기열 접근 정책을 SNS 토픽이 SQS 대기열에 쓰기할 수 있도록 허용해야 한다.

교차 리전 배송: 한 리전의 SNS 토픽이 다른 리전의 SQS 대기열에 메시지를 보내는 것이 가능하다.

### Application: S3 events to multiple queues

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/a5140e11-4aac-4712-b44b-42f0ba212ceb/Untitled.png)

S3 이벤트 규칙에는 제한이 있다.

⇒ 객체가 생성되고 객체의 접두사가 images/와 처럼 이벤트 형식이 조합될 경우 오직 한 개의 이벤트 규칙만 존재할 수 있다.

근데 만약 여러 개의 대기열로 동일한 S3 이벤트 알림을 보내고 싶다면?!?!

→ fan out 모델 사용하기

### Application: SNS to Amazon S3 through Kinesis Data Firehose

SNS에서 직접 amazon S3 로 데이터를 보낼 수 있다

→ SNS가 KDF와 직접적으로 통합되어 있기 때문에 어떤 서비스가 SNS 토픽으로 데이터를 전송할 수 있고 KDF가 그 정보를 받을 것이다.

### Amazon SNS - FIFO topic

토픽 메시지에 순서를 부여하는 것이다.

SQS FIFO랑 같은 기능을 갖는 것이다.

→ 그룹 id에 따라 메시지를 정렬하고

→ 중복된 id나 컨텐츠에 대해 중복 제거할 수 있고

SQS 표준과 FIFO 대기열 모두 구독자가 될 수 있다.

SQS FIFO와 같은 제한된 처리량을 갖는다.

### Amazon FIFO + SQS FIFO : Fan Out

fan out, 정렬, 중복제거가 필요할 때 사용한다.

### SNS - message filtering

SNS 토픽 구독자들에게 전송할 메시지를 필터링하는 JSON 정책이다.

이게 없다면 기본적으로 모든 메시지를 수신하게 된다.