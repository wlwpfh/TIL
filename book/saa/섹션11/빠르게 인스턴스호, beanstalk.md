## Instantiating Applications Quickly

애플리케이션을 빠르게 인스턴스화하는 방법

1. EC2 instance
    1. golden AMI를 사용할 수 있다. 이는 애플리케이션과 OS 종속성 등 모든 것을 사전에 설치하고 그것으로부터 AMI를 생성하는 것이다.
    2. 사용자 데이터 (bootstrap using user data) → 인스턴스를 부트스트랩(= 인스턴스가 처음 시작될 때 구성하는 것)할 수 있게 해준다.
    3. hybrid → golden AMI와 EC2 User Data를 하이브리드 혼합체로 할 수 있다.
2. RDS Databases
    1. 스냅샷으로부터 복원할 수 있으며 이는 데이터베이스에서 스키마와 데이터가 준비될 것이다.
3. EBS Volumes
    1. 스냅샷으로 EBS 볼륨을 복구할 수 있어서 포맷되지 않은 빈 디스크는 필요하지 않는다.


## Elastic Beanstalk

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/d2f37fde-fe52-42f4-87bf-e2f540818b86/Untitled.png)

public subnet / private subnet / data subnet의 형식으로 있었다.

동일한 애플리케이션을 계속 배포해야 한다면 이런 아키텍처를 똑같이 계속 만들기 힘들다.

beanstalk는 개발자 입장에서 애플리케이션을 AWS에 배포한다.

하나의 인스턴스에서 EC2, ASG, ELB, RDS 등 모든 컴포넌트를 재사용한다는 개념이다.

→ beanstalk는 관리형 서비스

용량 프로비저닝, 로드 밸런서 설정. 스케일링, 모니터링, 인스턴스 설정 등을 담당한다.

나는 코드만 담당하면 된다!

### 구성 요소

환경, 버전, 설정 등 애플리케이션으로 구성된다.

애플리케이션 자체 버전은 우리의 애플리케이션과 같게 된다.

환경 - 특정한 애플리케이션 버전을 실행하는 리소스들의 컬렉션이다.

- collections of AWS resources running an application version
- tier: 웹 서버 환경 티어, 워커 환경 티어
- 다양한 환경을 설정할 수 있다. (dev, test, prod)

순서: 애플리케이션 생성 → 버전 업로드 → 환경 시작 → 환경 라이프사이클 관리

### web server tier

여기서는 직접적으로 인스턴스에 접근하는 클라이언트가 없다.

메세지 대기열인 SQS Queueㄹ르 사용한다.

메세지는 SQS Queue로 전송되고 인스턴스는 워커가 된다.

왜냐하면 인스턴스들이 메세지를 큐에서 가져와서 처리하게 된다.

+) 워커 환경은 SQS 메세지의 개수에 따라 스케일링 될 것이다.

++) 웹 환경이 메시지를 워커 환경의 SQS Queue에 푸시하게 함으로써 웹 환경(기존의 아키텍쳐)과 워커환경을 하나로 모을 수 있다.

### Elastic Beanstalk Deployment Mode

1. 싱글 인스턴스

개발용으로 좋다.

하나의 인스턴스를 가진다. 탄력적 IP를 가진다.

RDS를 가진다.

1. 로드밸런서를 사용하는 고가용성 모드

실제 elastic beanstalk 모드로 스케일링을 하고 싶을 때 사용한다.

프로덕션 환경에 적합하다.