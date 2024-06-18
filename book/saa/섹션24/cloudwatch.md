## cloudwatch

### cloudwatch 지표 개요

aws의 모든 서비스에 대한 지표를 제공한다.

→ 계정에서 일어나는 모든 일을 모니터링 가능

지표는 모니터링의 변수

ex) ec2 → cpu utilization, networkIn 등이 있다.

지표는 이름 공간에 속하므로 각기 다른 이름공간에 속하므로 서비스당 이름공간은 하나이다.

→ 지표의 속성으로 측정 기준이 있다. instance id, 환경 등일 수 있다.

지표당 최대 측정 기준은 30개이다.

지표는 시간을 기반으로 하므로 타임스탬프가 있어야 하고 지표가 많아지면 cloudwatch 대시보드에 추가해 모든 지표를 한번에 볼 수 있다.

cloudwatch 사용자 지정 지표를 만들 수 있다. - 자체 지표

### cloudwatch  지표 스트림

cloudwatch 지표는 외부로 스트리밍할 수 있다.

cloudwatch 지표를 원하는 대상으로 지속적으로 스트리밍하면 거의 실시간으로 전송되고 지연 시간도 짧아진다.

→ 대상: amazon kinesis data firehose, 타사 서비스 제공자

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/d7e96abb-5e27-4b4d-ab87-fe3adb4cbf42/Untitled.png)

모든 이름공간에 속한 모든 지표를 스트리밍하거나 몇몇 이름공간의 지표만 필터링도 할 수 있다.

### cloudwatch 로그

aws에서 애플리케이션 로그를 저장할 수 있는 완벽한 곳이다.

1. 그룹 로그 먼저 정의해야 한다.

   : 보통은 애플리케이션을 나타내는 이름으로 한다.

1. 로그 그룹 안에 다수의 로그 스트림이 있다.

   : 이것은 애플리케이션 안의 로그 인스턴스나 특정 로그 파일, 특정한 컨테이너를 나타낸다.

1. 로그 만료 정책을 정의한다.

   : 영원히 만료되지 않는 것도 있고, 1일부터 10년까지도 있다.

1. 다양한 대상으로 전송할 수 있다.

   : amazon s3, kinesis data streams, kinesis data firehose, aws lambda, opensearch

1. 기본적으로 모든 로그가 암호화되어 있다.

   : 원한다면 자신의 키를 사용해서 자체적으로 kms 기반 암호화를 설정할 수 있다.

### 어떤 종류의 로그 데이터가 cloudwatch log로 갈까?

sdk, cloudwatch logs agent, cloudwatch unified agent를 사용해서 로그를 전송할 수 있다.

elastic beanstalk을 사용해서 애플리케이션에서 로그를 수집하고 직접 cloudwatch로 보낼 수 있다.

ecs는 로그를 컨테이너에서 곧바로 cloudwatch로 보낸다.

람다는 함수 자체에서 로그를 전송한다.

vpc flow logs는 우리의 vpc 메타데이터 네트워크 트래픽의 특정한 로그를 전송한다.

api gateway는 오는 모든 요청을 cloudwatch logs로 보낸다.

cloudtrail based on filter는 직접 필터에 기반한 로그를 전송한다.

route 53은 서비스에 대한 모든 dns 쿼리를 로깅한다.

### cloudwatch logs의 로그를 쿼리하려면 어떻게 해야 할까?

cloudwatch logs insight를 이용하면 된다.

1. cloudwatch logs 안에서 로그 데이터를 검색하고 분석할 수 있다.
2. 특정한 목적으로 제작된 쿼리 언어를 제공한다.
    - 쿼리를 제작하도록 해주는 모든 필드를 자동으로 cloudwatch logs가 자동으로 탐지한다.
3. 다른 계정에 있어도 한번에 다수의 로그 그룹을 쿼리할 수 있다.
4. 실시간 엔진이 아닌 쿼리 엔진이다.

→ 쿼리를 실행하면 과거 데이터만 쿼리하게 된다.

### cloudwatch logs - S3로 내보내기

모든 로그를 s3로 전송하는 배치 export에 사용된다.

완료될 때까지 12시간이 걸릴 수 있다.

내보내기를 시작하기 위한 api 호출을 CreateExportTask라고 부른다.

배치이기 때문에 실시간이나 근 실시간이 아니다.

### cloudwatch logs subscription

로그 이벤트들의 실시간 스트림을 얻게 되고 처리하여 분석할 수 있다.

→ 데이터를 kinesis data stream, kinesis data firehose, lambda로 보낼 수 있다.

subscription filter를 써서 전송 대상으로 전달하려는 로그 이벤트의 종류를 지정할 수 있다.

### cloudwatch logs aggregation multi account & multi region

다양한 계정과 리전에서 온 로그 데이터를 하나의 특정 계정에서 kinesis data streams 등을 통해 집계할 수 있다.

이걸 이제 다른 곳으로 보내거나 할 수 있다.

### cloudwatch logs subscription

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/bd7b4223-c7ab-4949-9a0d-110f8bbc98d1/Untitled.png)

### clouddwatch logs for EC2

기본저긍로 ec2에서 어떤 로그도 옮겨지지 않는다.

cloudwatch agent라는 프로그램을 실행하여 원하는 로그 파일을 푸시해야 한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/327809fa-d062-4931-a07c-736cee5750d4/Untitled.png)

인스턴스에 로그를 보낼 수 있도록 해주는 iam 역할이 있어야 한다.

에이전트는 온프레미스 환경에서도 셋업될 수 있다.

### clcoudwatch의 두가지 에이전트

둘 다 ec2 인스턴스나 온프레미스 서버 같은 가상 서버를 위한 것이다.

1. cloudwatch log agent - 옛날 버전, cloudwatch logs로만 보낼 수 있다.
2. cloudwatch unified agent - 프로세스나 ram 같은 추가적인 시슽메 단계 지표를 수집한다, cloudwatch logs애 로그를 보낸다, 지표와 로그를 통합한다, ssm parameter store를 사용하여 에이전트를 쉽게 구성할 수 있다.

   →중앙 집중식 환경 구성을 할 수 있다.


### cloudwatch unified agent- 지표

에이전트를 리눅스 서버에 설치하면 지표를 수집할 수 있다.

→ cpu, dis metrics, disk IO, ram, netstat, processes, swap space,

통합 cloudwatch 에이전트가 더 세부적이고 많은 지표를 수집한다는 것이다.

### cloudwatch 경보

알람은 메트릭에서 나오는 알림을 트리거하는 데 사용된다.

다양한 옵션으로 복잡한 알람을 설정할 수 잇따.

- 알림 상태
    1. ok: 알람이 트리거되지 않은 상태
    2. insufficient data: 알림이 상태를 결정하기 위한 충분한 데이터가 없는 의미
    3. alarm: 한도값을 초과한 상태
- 기간 - 알람이 얼마나 오랫동안 메트릭을 평가할지 나타낸다.
    1. 기간은 매우 짧거나 길 수 있다.

### cloudwatch 알람 주요 타깃

1. 정지, 종료, 재부팅, 복구 등 ec2 인스턴스에 대한 액션이다.
2. 오토 스케일링 액션을 트리거하는 것이다.
3. sns 서비스에 알림을 전송하는 것이다.

### cloudwatch alarm -  복합 알람

cloudwatch alarm은 하나의 매트릭에 적용된다.

→ 다수의 메트릭을 사용하고 싶다면 복합 알람을 사용해야 한다.

복합 알람은 실제 다수의 다른 알람들의 상태를 모니터링하고 그런 알람들은 각각 하나의 다른 메트릭에 의존할 수 있다.

and와 or 조건을 사용해서 검사하는 조건을 유연하게 다룰 수 있다.

복합 알람은 알람 노이즈를 줄이는데 유용하다. → 복잡한 복합 알람을 만들 수 있기 때문에

### ec2 instance 복구

- 상태 검사
    1. 인스턴스 상태 = ec2 vm을 검사한다.
    2. 시스템 상태 = 기본적인 하드웨어 검사한다.

특정한 ec2 인스턴스를 모니터링하게 된다.

만일 알람이 발생하면 ec2 인스턴스 복구를 시작해서 ec2 인스턴스를 한 호스트에서 다른 호스트로 옮길 수 있다.

알람 알림을 테스트하고 싶으면 set-alarm-state라는 cli를 호출할 수 있다.