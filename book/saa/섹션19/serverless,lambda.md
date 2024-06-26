### Serverless

새로운 서비스로, 이 서비스를 사용하는 개발자는 서버를 관리할 필요가 없다,

→ 서버가 없다 X, 서버를 관리/프로비저닝 할 필요가 없다 O

그냥 코드를 배치하기만하면 된다.

원래의 서버리스는 FaaS(=function as a service)를 뜻했지만 지그의 서버리스는 더 많은 걸 표현한다.

### Serverless in AWS

시나리오) 유저가 s3 버킷에서 정적 컨텐츠를 얻는데 웹사이트나 cloudfront, s3로 전달된다.

그리고 cognito 에 로그인하는데 사용자는 api gateway를 통해 rest api를 호출하고 api gateway는 lambda를 호출한다. lambda는 dynamoDB에서 데이터를 저장하고 회수한다.

---

## Lambda

EC2는 클라우드의 가상 서버이다.

→ 프로비저닝이 필요하여 프로비저닝을 할 CPU와 메모리 크기가 제한된다.

→ 지속적으로 실행되어야 한다.

→ 오토 스케일링 그룹으로 스케일링 할 수 있다.

lambda는 가상 함수이다.

→ 관리할 서버 없이 코드를 프로비저닝하면 함수가 실행된다.

→ 제한 시간이 있어 실행 시간이 짧다.

→ 온디멘드로 실행된다.

⇒ lambda를 사용하지 않으면 람다 함수가 실행되지 않고 비용도 실행될 때만 나간다.

→ 스케일링이 자동화되어 있다.

### lambda 장점

1. 가격 책정이 쉽다.

→ 호출 횟수와 컴퓨팅 시간 당 낸다.

→ 프리티어한테도 넉넉하게 준다.

1. 다양한 aws 서비스와 통합된다.
2. lambda에 여러가지 프로그래밍 언어를 사용할 수 있다.
3. cloudwatch와의 모니터링 통합이 쉽다.
4. 함수당 더 많은 리소스를 프로비저닝하려면 함수당 최대 10기가의 램을 프로비저닝할 수 있다.
5. 함수의 메모리를 증가시키면 cpu와 네트워크 품질도 같이 향상된다.

### lambda를 지원하는 언어

- node.js, python, java, c#, golang, c#, ruby, custom runtime api, …

+) lambda 컨테이너 이미지

: 컨테이너 이미지 자체가 lambda의 런타임 api를 구현해야 한다.

: ECS, fargate는 계속 임의의 도커 이미지를 실행할 때 더 많ㅇ ㅣ사용된다.

### lambda와 aws 서비스와의 통합

api gateway는 rest api를 생성하고 람다 함수를 호출한다.

kinesis lambda를 이용해 바로 데이터를 변환한다.

dynamoDB는 트리거를 생성할 때 사용된다. 데이터베이스에 무슨 일이 생기면 람다 함수가 실행된다.

S3는 언제든 람다 함수를 실행한다.

cloudfront용 람다는 Lambda@Edge이다.

cloudwatch event, event bridge에서는 aws 인프라에 뭐가 생기고 이를 대응하려 할 때 람다 함수를 사용한다.

cloudwatch log는 어디든 해당 로그를 스트리밍한다.

sns로 알림과 sns 토픽에 대처할 수 있다.

sqs로는 sqs 대기열 메시지를 처리할 수 있다.

cognito는 사용자가 행동을 할 때 실행된다.

### ex) Serverless 섬네일 생성

S3 버킷이 있고 여기에 섬네일을 바로 생성하고 싶다.

1. s3에 새 이미지가 업로드되는 이벤트가 생긴다.
2. s3 이벤트 알림을 통해 lambda 함수가 작동된다.
3. lambda 함수에는 섬네일을 생성하는 코드가 있다.
4. 그 섬네일은 같거나 다른 s3 버킷으로 푸시 및 업로드된다.
5. 람다 함수는 몇 데이터를 dynamoDB에 넣을 수 있다. - 이미지의 메타 데이터 저장

### ex) Serverless CRON Job

CRON - EC2 인스턴스에서 작업을 생성하는 방법이다.

주기를 지정한다. 이것은 가상 서버에 실행해야 한다.

1. cloudwatch나 eventbridge 규칙을 만든다.
2. 한 시간마다 작동되게 설정한다.
3. 한 시간마다 람다 함수와 통합된다
4. 실행된다!

### 람다 가격 책정 예시

- 호출당 청구

  첫 백만건은 무료, 이후 백만건마다 2센트

- 기간당 청구

## lambda 한도 - 지역마다

한도에는 실행 한도와 배포 한도가 있다.

1. 실행 한도

메모리 할당량: 128mb ~ 10gb (1mb씩 증가)

최대 실행 시간: 15분

환경변수: 4kb

큰 파일을 가져올 때 사용할 수 있는 임시 공간: 512mb ~ 10gb

람다 함수는 최대 1000개까지 동시 실행 가능

1. 배포 한도

압축 시 최대 크기 50mb

압축하지 않았을 때: 250mb

시작할 때 큰 파일이 있다면 /tmp 디렉터리 사용하면 좋다.

환경변수: 4kb

### lambda snapstart

lambda 함수의 성능을 높이기 위한 기능이다.

자바11 이상에서 실행되는 lambda 함수들을 추가 비용 없이 성능을 최대 10배 높여준다.

lambda 호출은 몇개의 생명주기 단계가 있다.

; snapstart 비활성화되어 있다. → 자바에서 실행중인 lambda 함수가 있다면 → 이 함수가 호출되었을 때 자바 코드가 초기화된 후에 호출되고 종료된다.

; snapstart가 활성화되어 있다면 → 함수가 미리 초기화된 상태에서 호출된다. → 호출단계로 넘어가서 종료 단계로 넘어간다.

(처음에 함수 초기화가 없다.)

- 새로운 lambda 버전을 발행할 때

→ lambda가 함수를 초기화한다.

→ 메모리와 초기화된 함수의 디스크 상태의 스냅샷이 생성된 후

→ 이 스냅샷이 저지연 액세스를 위해 캐시될 것이다.

### Edge에서의 사용자 지정

보통은 함수와 애플리케이션을 특정 리전에서만 배포한다.

모던 애플리케이션에서는 애플리케이션에 도달하기 전에 엣지에서 로직을 실행하도록 요구하기도 한다.

⇒ 엣지 함수

- cloudfront 배포에 연결하는 코드이다.
- 이 함수는 사용자 근처에서 실행하여 지연 시간을 최소화하는 것이 목적이다.

그리고 cloudfront에는 두 종류의 함수가 있다.

→ cloudfront function

→ lambda@edge

암튼 엣지 함수를 사용하면 서버를 관리할 필요가 없다. 전역으로 배포되기 때문에

ex) CDN

사용한 만큼만 지불하고 완전 서버리스!

### CloudFront functions & lambda**@edge 사용 사례**

1. 웹사이트 보안과 프라이버시
2. 엣지에서의 동적 웹 애플리케이션
3. 검색 엔진 최적화
4. 오리진 및 데이터 센터 간 지능형 경로
5. 엣지에서의 봇 완화
6. 엣지에서의 실시간 이미지 변환
7. …

### CloudFront functions

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/d18c0c71-d5da-4e4e-bb18-50c27934ce27/Untitled.png)

자바스크립트로 작성된 경량 함수이다.

**뷰어 요청과 응답을 수정할 때에만 사용된다.**

→ cloudfront가 뷰어(클라이언트)로부터 요청을 받은 다음에 뷰어 요청을 수정할 수 있다. - viewer request에 대해서

→ cloudfront가 뷰어에게 응답을 보내기 전에 뷰어 응답을 수정할 수 있다. → viewer response에 대해서

확장성이 높고 지연 시간에 민감한 CDN 사용자 지정에 사용된다.

시작 시간은 1밀리초 미만, 초당 백만개의 요청을 처리한다.

cloudfront의 네이티브 기능으로 모든 코드가 cloudfront에서 직접 관리된다.

**고성능, 고확장성이 필요할 때 사용된다.**

### lambda@edge

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/d18c0c71-d5da-4e4e-bb18-50c27934ce27/Untitled.png)



함수는 node.js나 python으로 작성한다.

초당 수천개의 요청을 처리할 수 있다.

모든 cloudfront 요청 및 응답을 변경할 수 있다.

→ cloudfront가 뷰어(클라이언트)로부터 요청을 받은 다음에 뷰어 요청을 수정할 수 있다. - viewer request에 대해서

→ cloudfront가 오리진에 요청을 전송하기 전에 수정 가능 → origin request에서

→ cloudfront가 오리진에서 응답을 받은 후에 수정 가능 → origin response에서

→ cloudfront가 뷰어에게 응답을 보내기 전에 뷰어 응답을 수정할 수 있다. → viewer response에 대해서

함수는 us-east-1 리전에서만 작성할 수 있다.

= cloudfront 배포를 관리하는 리전과 같은 리전이다.

### 둘의 차이

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/f21233f3-3c16-4e53-90df-168468ddbd0f/Untitled.png)

런타임 지원이 가장 눈에 띈다.

### 둘의 차이 - 사용 사례로

- CloudFront functions

→ 캐시 키를 정규화한다.

→ 요청 속성을 변환하여 최적의 캐시 키를 생성한다.

→ 헤더를 조작한다.

→ 요청이나 응답에 http 헤더를 삽입,수정,삭제하도록 한다.

→ url을 다시 쓰거나 리디렉션한다.

→ 요청 허용 또는 거부

→ jwt 생성/검증하는 요청 인증 및 권한 부여에 사용된다.

⇒ 이 모든 작업이 1밀리초 이내에 끝난다.

- lambda@edge

→ 긴 실행 시간

→ cpu와 메모리가 증가한다.

→ 여러 라이브러리에 코드를 의존시킬 수 있다.

→ 네트워크 액세스를 통한 외부 서비스에서의 데이터 처리

→ 대규모 데이터 통합 가능

→ 파일 시스템이나 http 요청에도 바로 액세스 가능

### lambda 네트워킹 기초

기본적으로 lambda 함수를 시작하면 우리의 vpc 외부에서 시작된다.

→ 우리는 vpc 내에서 리소스에 액세스할 권한이 없다.

→ rds, elasticcache, 내부 elb를 시작하면 lambda 함수가 헤당 서비스에 액세스할 수 없다.

→ 그래도 인터넷 퍼블릭 api에 액세스하는 것은 가능하다.

→ dynamoDB에 접근할 수 있는데 이는 dynamoDB가 aws 클라우드의 퍼블릭 리소스이기 때문이다.

근데 이 문제를 해결하면 vpc에서 lambda 함수를 시작하면 된다!

### lambda in VPC

VPC id, lambda 함수를 시작하려는 서브넷을 지정하고 lambda 함수에 보안 그룹을 추가해야 한다.

→ 그러면 lambda가 서브넷에 ENI(Elastic Network Interface)를 생성해 vpcpd서 실행되는 애플리케이션에 액세스할 수 있다.

### lambda with RDS proxy

vpc에서 lambda를 사용하는 대표적인 사례이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/e64172c4-b97b-47db-b699-8606e9642fb5/Untitled.png)

RDS 데이터베이스가 프라이빗 서브넷에 있어도 lambda 함수로 직접 해당 db에 접근할 수 있다.

→ 그런데 이 방식으로 직접 액세스하면 큰 문제가 발생한다.

→ lambda 함수의 수가 생성되었다 사라지길 반복하면 개방된 연결이 너무 많아져서 데이터베이스의 로드가 상승해 시간 초과 등의 문제로 이어진다.

⇒ 이걸 해결하는 방법은 rdx proxy를 시작하는 것이다.

프록시가 연결을 한 곳으로 모아 데이터베이스 인스턴스 연결 수를 줄인다.

lambda 함수는 프록시에 연결되고 프록시는 데이터베이스로 연결되어 문제가 해결된다.

프록시의 장점

1. 데이터베이스 연결의 풀링과 공유를 통해 확장성을 향상시킨다.
2. 장애가 발생할 경우 장애 조치 시간을 66%까지 줄여 가용성을 향상시키고 연결을 보존한다.
3. rds proxy 수준에서 iam 인증을 강제하여 보안을 높일 수 있고 자격 증명은 secrets manager에 저장된다.

**lambda 함수가 rds 프록시에 연결할 수 있으려면 우리의 vpc에서 lambda 함수를 시작해야 한다!**

### invoking(호출하다) lambda **with RDS & Aurora**

- 어떤 경우에 데이터베이스 인스턴스 안에서 람다 함수를 호출할 수 있다.

→ 그러면 우리는 데이터베이스 안에서 일어나는 데이터 이벤트를 처리할 수 있게 된다.

→ 이건 rds for progreSQL, aurora MYSQL에 지원된다.

개념의 예시)

1. 사용자가 이벤트 데이터를 rds의 등록 테이블에 저장한다.
2. rds는 우리의 람다 함수를 직접 호출하도록 설정된다.
3. 이 람다 함수는 사용자에게 이메일을 보낼 수 있고 사용자는 그 메일을 받게 된다.

⇒ 이걸 하기 위해서는 RDS 데이터베이스 인스턴스로부터 람다 함수로 오는 인바운드 트래픽을 반드시 허용해야 한다.

(모든 방식으로 오는 트래픽을 허용해야 한다. 왜냐면 우리의 람다가 퍼블릿 인터넷에 있는 경우에도 허용해야 함)

+) 데이터베이스 인스턴스가 람다 함수를 호출할 것이기에 람다 함수를 호출할 필수적인 권한을 갖고 있어야 한다.

→ 데이터베이스 인스턴스가 적절한 iam 정책을 갖고 있는지 확인해야 한다.

### RDS Event notification

이 알림들은 aws안에서 이뤄진다.

데이터베이스 인스턴스 자체에 대한 정보를 알려주는 알림들이 있다.

데이터에 대한 정보는 전혀 없다.

전달시간이 최대 5분인 근 실시간 이벤트를 받는다.

→ 받아서 sns에 전송하거나 event eventbridge에서 가로챌 수 있다.