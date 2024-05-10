## Amazon dynamoDB

완전 관리형 데이터베이스로 데이터가 다중 az 간에 복제되므로 가용성이 높다.

클라우드 네이티브이다.

noSQL 데이터 베이스이다. - 관계형 데이터베이스가 아니다 - 트랜잭션 지원 기능이 있다.

dynamoDB를 이용하면 방대한 워크로드로 확장이 가능하다.

→ 데이터베이스가 내부에서 분산되기 때문에

초당 수백만개의 요청을 처리하고 수조개의 행을 처리한다.

**성능은 한 자릿수 밀리초를 자랑하고 일관성이 높다.**

보안과 관련된 모든 기능은 iam과 통합되어 있다.

비용이 적게 들고 오토스케일링 기능이 탑재되어 있다.

유지 관리/패치 없이도 항상 사용할 수 있다.

테이블의 종류: 액세스가 빈번한 데이터에는 standard 클래스, 액세스가 빈번하지 않은 데이터는 IA 테이블 클래스에 저장한다.

### dynamoDB **기초**

dynamoDB는 테이블로 구성되며 데이터베이스를 생성할 필요가 없다.

→ 데이터베이스가 이미 존재하는 서비스이다.

테이블을 생성하면 각 테이블에 기본 키가 부여된다. - 기본 키는 생성 시 결정된다.

각 테이블에 데이터를 추가한다. → 행을 무한히 추가할 수 있다.

각 항목은 속성을 가지며 속성은 열에 표시된다. (속성은 나중에 추가 가능, null 가능)

항목의 최대 크기는 400kb → 큰 객체를 저장할 때 적합하지 않다.

다양한 데이터 유형을 지원한다.

→ scalar type: string, number, binary, boolean, null

→ document types: list, map

→ set types: String set, number set, binary set

**스키마를 빠르게 전개해야 할 때 dynamoDB를 선택하면 좋다!**

### dynamoDB - 읽기/쓰기 용량 모드 설정

- 테이블 용량 관리 방식을 제어하는 데는 두가지 모드가 있다.
1. 프로비저닝 모드(기본)

   미리 용량을 프로비저닝한다.

   초당 읽기/쓰기 요청 수를 예측해서 미리 지정하면 테이블 용량이 된다.

   미리 용량을 계획하고 프로비저닝 된 rcu/wpc만큼 지불한다.

   미리 용량을 계획한 경우에도 오토스케일링 기능이 있어 테이블 로드에 따라 rcu, wcu를 늘리거나 줄일 수 있다.

   천천히 비용 절감을 원할 때 적합하다.ㅇㅇ

2. 온디멘드 모드

   읽기/쓰기 용량이 워크로드에 따라 자동으로 확장된다.

   미리 용량 계획을 하지 않아 rcu, wcu 개념 자체가 없다.

   정확히 사용한 만큼의 비용이 나온다.

   워크로드를 예측할 수 없거나 급격히 증가하는 경우에 유용하다.


### **dynamoDB Accelerator (DAX)**

dynamoDB를 위한 고가용성의 완전 관리형, 무결절(seamless) 인메모리 캐시이다.

dynamoDB 테이블에 읽기 작업이 많을 때 dax 클러스터를 생성하고 데이터를 캐싱하여 읽기 혼잡을 해결한다.

**dax는 캐시 데이터에 마이크로초 수준의 지연 시간을 제공한다.**

dax 클러스터는 기존 dynamoDB api와 호환되어 애플리케이션 로직을 변경할 필요가 없다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/838d9d00-b16d-46a0-b027-02bdf2ad3e59/Untitled.png)

캐시의 기본 ttl은 5분으로설정되어 있다. (변경가능)

### dax vs ElastiCache

dax는 dynamoDB 앞에 있고 개별 객체 캐시와 쿼리와 스캔 캐시를 처리하는 데 유용하다.

집계 결과를 저장할 때에는 elastiCache가 좋다.

dynamoDB는 대용량의 연산을 저장할 때 좋다.

두 서비스는 상호 보완적인 성격을 띈다.

dynamoDB에 캐싱 솔루션을 추가할 때 보통 dax를 사용한다.

### dynamoDB - stream processing

스트림 처리도 가능하다.

테이블의 모든 수정 사항 (삭제,업데이트,생성)에 대한 스트림을 생성할 수 있다.

ex) 사용자 테이블에 새로운 사용자가 등록되었을 때 환영 이메일을 보내는 등

ex) 실시간으로 사용 분석

ex) 크로스 리전 복제 향상

ex) dynamo 테이블에 변화로 aws lambda 호출하기

- dynamoDB stream

→ 스트림은 보존 기간이 24시간

→ 소비자수 제한

→ lambda 트리거와 함께 사용되면 좋다.

→ 자체적인 읽기를 실행하려면 dynamoDB stream Kinesis adapter 사용.

- kinesis data stream

→ 보존 기간 1년

→ 더 많은 소비자 수

→ 데이터 처리하는 방법이 더 많다.

### dynamoDB streams

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/993a11e9-c68d-47db-8ce5-a690a0670b9f/Untitled.png)

만약 스트림을 kinesis data stream으로 했다면 kinesis data firhose를 선택 가능하다.

+) 분석 목적으로 데이터를 amazon redshift로 전송하고 데이터 아카이빙을 하려면 s3로 전송

만약 스트림을 dynamoDB stream으로 했다면 processing layer를 둘 수 있다.

EC2 인스턴스에서 애플리케이션을 실행하려면 KCL 어댑터나 람다함수를 사용한다.

→ 후에 sns로 가거나 필터링하거나 변환할 수 있다.

### dynamoDB global table

global table = 여러 리전 간에 복제가 가능한 테이블

테이블 간 양방향 복제가 가능하다.

1. 복수의 리전에서 짧은 지연 시간으로 접근 가능하다.
2. 다중 활성 복제가 가능하다
3. 애플리케이션이 모든 리전에서 테이블을 읽고 쓸 수 있다.
4. 글로벌 테이블을 활성화하려면 dynamoDB 스트림을 활성화해야 한다.

### dynamoDB - time to live

만료 타임스탬프가 지나면 자동으로 항목을 삭제하는 기능이다.

사용사례: 최근 항목만 저장하도록 하거나 웹 세션 핸들링.. 데이터 삭제 규정

### dynamoDB - 재난 복구에 대한 백업

지정 시간 복구(PITR)을 사용해서 지속적인 백업을 할 수 있다.

→ 활성화를 선택할 수 있고 35일동안 지속된다.

→ 활성화하면 백업 기간 내에는 언제든 지정 시간 복구를 실행할 수 있다.

→ 복구를 진행할 경우 새로운 테이블을 생성한다.

온디멘드 백업

→ 더 긴 백업 옵션으로 직접 삭제할 때까지 보존한다.

→ dynamoDB의 성능이나 지연 시간에 영향을 주지 않는다.

→ 백업으로 복구를 진행하면 새로운 테이블이 생성된다.

### dynamoDB - S3 간 통합

1. s3에 테이블을 내보낼 수 있다.

→ 이를 위해서 PITR을 활성화 해야한다.

→ 지속적 백업을 활성화한 상태여서 35일 이내 어느 시점으로든 보낼 수 있다.

→ 테이블의 읽기 용량이나 성능에 영향을 주지 않는다.

→ 오히려 데이터 분석을 수행할 수 있다.

→ 감사 목적으로 스냅샷 확보 가능

→ 데이터를 dynamoDB로 다시 가져오기 전에 데이터 ETL 등 대규모 변경을 실행할 수 있다.

→ 내보낼 때는 dynamoDB json이나 ion 형식을 이용한다.

1. s3에서 테이블 가져오기

→ s3에서 csv, json, ion 형식으로 보낸 다음 새로운 dynamoDB 테이블을 생성

→ 쓰기 용량을 소비하지 않고 새로운 테이블이 생성된다.

→ 가져올 때 발생한 오류는 모두 cloudwatch logs에 기록된다.

+) 암튼 dynamoDB 테이블을 s3로 보내고 쿼리를 수행하려면 aamzon athena 엔진을 사용한다.

---

### Example: Building a Serverless API

클라이언트도 람다 함수를 이용하고 싶다면 API gateway를 이용하면된다.

→ aws의 서버리스 서비스로 rest api를 생성할 수 있어 클라이언트가 퍼블릭 액세스할 수 있다.

→ lambda 함수에 요청을 프록시한다.

## API gateway

- 람다 + API gateway = 완전 서버리스 애플리케이션 구축으로 인프라 관리가 필요 없다.
- websocket 프로토콜을 지원
- api 버저닝을 핸들링한다.
- 여러 환경을 핸들링한다. - dev/test/prod
- 보안에도 활용할 수 있다. - 인증. 권한 부여 등..
- api 키를 생성할 수 있다.
- API gateway에 클라이언트 요청이 과도할 때 요청을 스로틀링할 수 있다.
- swagger나 open api를 사용하여 신속히 api를 정의하여 가져올 수 있다.
- 요청과 응답을 변형하거나 유효성을 검사해 올바른 호출이 실행되게 할 수 있다.
- sdk나 api 스펙을 생성하거나 api 응답을 캐시할 수 있다.

### API gateway 의 통합 지원

1. lambda 함수
    - 람다 함수를 지연 호출한다.
    - 람다 함수를 사용하는 rest api를 완전 서버리스 애플리케이션에 노출시키는 일반적이고 간단한 방법이다.
2. http
    - 백엔드의 http 엔드포인트를 노출시킨다.
    - 온프레미스에 http api가 있거나 alb가 있을 때 API gateway를 이용하면 속도제한 기능. 캐싱, 인증 등 사용할 수 있다
3. aws service
    - 어떤 aws api라도 노출시킬 수 있다.
    - 예를 들어 단계 함수 워크플로우를 시작할 수 있고, SQS에 메시지를 게시할 수 있다.

   → 인증을 추가하거나 api를 퍼블릭으로 배포하거나 속도제한을 추가하기 위해 통합하는 것이다.


### API gateway - aws 서비스에 API gateway 사용하는 예시

1. kinesis data streams

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/2bd8685c-f1cc-43fc-955f-24f94ac90233/Untitled.png)

클라이언트와 kinesis 사이에 API gateway를 둔다.

그러면 api gateway가 kinesis로 보내고 서버를 관리할 필요가 없다.

→ API gateway를 통해 aws 서비스를 외부에 노출시킨다!

### API gateway - endpoint type (배포 방법)

1. 엣지 최적화 (기본 유형)
- 글로벌 클라이언트 용이다.
- 모두 전세계의 누구나 가능하다.
- 모든 요청이 cloudfront 엣지 로케이션을 통해 라우팅되어 지연 시간이 개선된다.
- API gateway는 생성된 리전에 위치하지만 모든 cloudfront 엣지 로케이션에서 액세스할 수 있다.
1. 리전 배포
- 모든 사용자는 API gateway를 생성한 리전과 같은 리전에 있어야 한다.
- 자체 cloudfront 배포를 생성할 수 있다.
1. private
- vpc 내에서만 액세스할 수 있다.
- eni 같은 인터페이스 vpc 엔드포인트를 사용해서 접근할 수 있다.
- API gateway에 액세스를 정의할 때는 리소스 정책을 사용한다.

### API gateway - 보안

1. 사용자를 식별하는 방법

→ iam 역할 사용하기 - 내부 애플리케이션에서 유용하다.

→ cognito - 모바일/웹 애플리케이션의 외부 사용자에 대한 보안 조치

→ 사용자 지정 권한 부여자 - 자체 로직 실행

1. HTTPS 보안

사용자 지정 도메인 이름을 aws Certificate Manager과 통합할 수 있다.

→ 엣지 최적화 엔드포인트를 사용할 경우, 인증서는 us-east-1에 있어야 한다.

→ 리전 엔드포인트를 사용할 경우, 인증서는 API gateway 단계와 동일한 리전에 있어야 한다.

→ route53에  cname이나 a별칭 레코드를 설정해 도메인 및 API gateway를 가리키도록 해야한다.

---

## Step functions

: 서버리스 워크플로우를 시각적으로 구성할 수 있는 기능이다.

: 주로 람다 함수를 오케스트레이션하는 데 사용한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/4fbb8c58-d077-44ef-a0c4-97cb93b18d4b/Untitled.png)

1. 그래프를 만들고 각 그래프 단계별로 해당 결과에 따라 다음으로 수행하는 작업이 뭔지 정의한다.
2. 제공하는 기능: 시퀀싱, 병행 실행, 조건 설정, 타임아웃, 에러 처리 등등
3. 람다 함수 뿐만 아니라 ec2, ecs, 온프레미스 서버, api gateway, SQS queue와도 연동할 수 있다.
4. 워크플로우에 사람이 개입해 승인을 해야만 진행되는 단계를 설정할 수 있다.

---

## amazon Cognito

: 사용자에게 웹 및 모바일 앱과 상호 작용할 수 있는 자격 증명을 부여한다.

( 이 사용자들은 aws 계정 외부에 있다.)

aws 외부의 웹/모바일 앱 사용자를 대상으로 한다!

- 하위 서비스
1.  cognito user pools

    : 앱 사용자에게 가입 기능을 제공

    : api gateway 및 애플리케이션 로드 밸런서와 원활한 통합

1. cognito identity pool = 페더레이션 자격 증명

   : 앱에 등록된 사용자에게 임시 aws 자격 증명을 제공해서 일부 aws 리소스에 직접 접근하게 할 수있게 해준다.

   : cognito 사용자 풀과 활발하게 통합된다.

### cognito 사용자 풀

웹, 모바일 앱을 대상으로 하는 서버리스 사용자 데이터베이스이다.

사용자 이름, 이메일, 비밀번호 조합으로 간단한 로그인 절차를 정의할 수 있다.

비밀번호 재설정 기능 / 이메일 및 전화번호 검증 / 멀티팩터 인증이 가능

페북이랑 구글과 통합 가능, 소셜 로그인 가능

api gateway나 alb와 통합된다.

ex) 사용자는 cognito 사용자 풀에 접속해서 토큰을 받고 검증을 위해 토큰을 api gateway에 전달한다.

확인이 끝나면 사용자 자격 증명으로 변환되어 람다 함수로 전달된다. 람다는 처리할 사용자가 잘 인증된 사용자라는 걸 인식하게 된다.

### cognito 자격 증명 풀 = 페더레이션 자격 증명

사용자에게 자격 증명을 제공하지만 api gateway나 alb를 통해 애플리케이션에 접근하지 않고 임시 자격 증명을 이용해 aws 계정에 직접 접근한다.

→ 사용자는 cognito 사용자 풀 내의 사용자. 타사 로그인이 될 수 있다.

api gateway를 통해 직접 서비스에 접근할 수 있다.

자격 증명에 적용되는 iam 정책은 cognito 자격 증명 풀 서비스에 사전 정의되어 있다.

user_id를 기반으로 사용자 정의하여세분화된 제어를 할 수 있다.

원한다면 기본 iam 역할을 정의할 수 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/09c35d1c-c4b4-400f-8b5d-35515c147ab3/Untitled.png)

### cognito identity pools row lowel security in dynamoDB

dynamoDB에 행 수준 보안을 설정할 수 있다.