### docker란?

앱 배포를 위한 소프트웨어 개발 플랫폼이다.

→ 컨테이너 기술

( 컨테이너에 앱이 패키징되어 있다. 컨테이너는 표준화되어 있어서 어느 운영체제에서나 실행할 수 있다.)

### docker image 저장 위치

→ 도커 리포지토리에 저장된다.

1. docker hub

퍼블릭 리포지토러로, 많은 기술에 맞는 기본 이미지를 찾을 수 있다.(ubuntu, mysql, …)

1. amazon ECR (Elastic Container Registry)

프라이빗 리포비토리

퍼블릭은 amazon ECR Public Gallery라 불리는 옵션이 있음

### docker vs Virtual Machine

리소스가 호스트와 공유되어 한 서버에서 많은 컨테이너를 실행시킬 수 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/cf0483ee-e7a4-4d8c-b2f8-72e997a5a622/Untitled.png)

도커가 가상머신보다 덜 안전하지만 하나의 서버에 많은 컨테이너를 실행할 수 있다.

### docker 시작하는 방법

Dockerfile 작성한 후에 build하면 이미지가 된다. → 이미지를 푸시해서 docker repository에 넣는다.

이 이미지를 pull 받아서 실행하면 컨테이너가 된다. 실행하면 Dockerfile에서 작성한 코드가 실행된다!

---

## ECS - EC2 시작 유형

- ECS = Elastic Container Service
- aws에서 컨테이너를 실행 == ECS 클러스터에 ECS 태스크를 실행한다
- EC2 시작 유형으로 EC2 클러스터를 사용할 때 인프라를 직접 프로비저닝하고 유지해야 한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/d0e830d7-7e6c-4bb0-9254-700029337b0b/Untitled.png)

ECS 및 ECS 클러스터는 여러 EC2 인스턴스로 구성된다.

EC2 인스턴스는 각각 ECS 에이전트를 실행해야 한다.

그럼 ECS 에이전트가 각각의 EC2 인스턴스를 amazon ECS 서비스와 지정된 ECS 클러스터에 등록한다.

이후에 ECS 태스크를 수행하기 시작하면 aws가 컨테이너를 실행하거나 멈출 것이다.

도커 컨테이너는 미리 프로비저닝한 EC2 인스턴스에 위치한다.

### Amazon ECS - Fargate 시작 유형

인프라를 프로비저닝하지 않아 관리할 EC2 인스턴스가 없다.

→ 서버리스!

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/003bc4df-d5fb-4745-88c6-a547b5b3bf62/Untitled.png)

- ECS 클러스터가 있을 때 ECS 태스를 정의하는 걸 생성하면 필요한 cpu나 ram에 따라 ECS 태스크를 aws가 대신 실행한다.

→ 새 도커 컨테이너를 실행하면 일단 실행된다. 확장하려면 태스크 수만 늘리면 된다.

(EC2 인스턴스를 관리할 필요가 없다.)

### ECS - IAM Role for ECS

- EC2 시작 유형을 사용한다면?

  → EC2 인스턴스 프로파일을 생성한다.

  ECS 에이전트만이 EC2 인스턴스 프로파일을 사용한다.

  그 EC2 인스턴스 프로파일을 이용하여 api 호출한다.

  인스턴스가 저장된 ECS 서비스가 CloudWatch 로그에 api 호출해서 컨테이너 로그를 보내고 ECR로부터 도커 이미지를 가져온다.

  Secrets Manager/ SSM Parameter Store에서 민감 데이터를 참고한다.

- ECS 태스크 역할

  → Ec2와 fargate 시작 유형에 모두 해당된다.

  각 테스트에 대한 특정 역할을 만들 수 있다.

  → 이렇게 하면 역할이 다른 각자 ECS 서비스에 연결할 수 있어서

  ECS 서비스의 태스크 정의에서 태스크의 역할을 정의한다.


### ECS - load balancer 통합

ALB는 대부분의 사용 사례를 지원하는 좋은 옵션이다.

NLB는 처리량이 많거나 높은 성능이 요구될 때에 권장된다. + aws private link와 같이 사용할 때 권장

ELB는 사용할 수는 있지만 권장되지 않는다.

### ECS - 데이터 지속성

이를 알아볼려면 데이터 볼륨이 필요하다.

그 중 EFS에서 자주 사용된다.

시나리오) EC2 태스크에 파일 시스템을 마운트해서 데이터를 공유하려 한다.

→ 그러면 EFS 파일 시스템을 사용하는 것이 좋다.

이거는 EC2, fargate 시작 유형 모두 호환된다. + EC2 테스트에 파일 시스템을 직접 마운트할 수 있다.

어느 az에 실행되는 태스크든  EFS에 연결되어 있으면 같은 데이터를 공유할 수 있고 원한다면 파일 시스템을 통해 다른 태스크와 연결할 수 있다.

⇒ fargate로 서버리스 방식 +  EFS (파일 시스템의 지속성을 위해) 이 조합이 권장된다!

+) 추가! S3는 ECS 태스크에 파일 시스템으로 마운트될 수 없다.

### ECS Service Auto Scaling

aws의 auto scaling이란 서비스를 사용하면 세 개의 지표에 대해 확장이 가능하다.

1. ECS 서비스의 cpu 사용률 확장
2. ECS 서비스의 ram 확장
3. ALB 관련 지표인 타켓당 요청 수 확장

- 다양한 오트 스케일링을 설정할 수 있다.
1. 타겟 트래킹 스케일링 : 특정 타켓 추적
2. 단계 스케일링
3. 미리 ECS 서비스 확장을 설정하는 예약 스케일링

- EC2 시작 유형이라면 태스크 레벨에서의 ECS 서비스 확장이 EC2 인스턴스 클러스터의 확장과 다르다!

ECS service auto scaling(task level) ≠ EC2 auto scaling (EC2 instance level)

- EC2 오토스케일링이 필요하지 않다면 fargate를 사용하는 것이 서비스 오토 스케일링에 도움된다.

### EC2 시작 유형 - auto scaling EC2 instances

스케일링의 방법 중 하나로 오토 스케일링 그룹이 있다.

→ cpu 사용률에 따라 asg를 확장한다.

→ EC2 인스턴스를 추가 생성한다.

아니면 ECS 클러스터 용량 공급자를 설정할 수 있다.

→ 새 태스크를 실행할 용량이 부족하면 자동으로 asg를 확장한다.

→ 오토 스케일링 그룹과 함께 사용된다.

→ ram/cpu 부족 시 EC2 인스턴스가 추가된다.

### ECS tasks invoked by Event Bridge

event bridge에 의해 호출된 ECS 태스크가 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/fb5d4223-8ece-4f4a-a1eb-36cdcd8931e2/Untitled.png)

사용자는 객체를 s3 버킷에 업로드한다.

버킷은 event bridge와 통합되어 모든 이벤트를 전송할 수 있다.

event bridge는 항상 ECS 태스크를 실행하기 위한 규칙을 갖고 있을 수 있다.

ECS 테스크가 생성된다. + ECS 테스크 역할

태스크 자체로부터 직접 객체를 받고/처리하고 결과를 dynamoDB로 전송할 수 있다.

### ECS tasks invoked by Event Bridge Schedule

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/82dce443-e126-4afc-9afa-9532096012f5/Untitled.png)

1시간마다 트러거되는 규칙을 스케줄링한다.

이 규칙은 fargate에서 ECS 태스크를 실행한다.

(1시간마다 fargate 클러스터 안에서 새로운 태스크가 생성된다.)

그리고 배치 프로세싱을 통해 한시간마다 수행할 수 있다.

### ECS - intercept stopped tasks using EventBridge

eventBridge를 사용해서 ECS 클러스터 안에서 이벤트를 가로채는 형태의 통합도 있다.

클러스터에서 나가거나 시작되는 모든 태스크는 eventBridge에서 이벤트로 트러거될 수 있다.

이 이벤트로 SNS를 트리거하여 메일이 보내질 수 있다.