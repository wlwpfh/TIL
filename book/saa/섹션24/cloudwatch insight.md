## cloudwatch 서비스의 인사이트 유형

### cloudwatch container insights

컨테이너로부터 지표와 로그를 수집, 집계, 요약하는 서비스이다.

ecs, eks, kubernetes platforms on EC2, fargate에서 사용가능하다.

⇒ 수집해서 cloudwatch에 세분화된 대시보드를 만들 수 있다.

+) cloudwathc container insight를 amazon eks, kubernetes platforms on EC2에서 사용할 경우, 컨테이너화된 버전의 cloudwatch 에이전트를 사용해야 컨테이너를 찾을 수 있다.

### cloudwatch lambda insight

람다에서 실행되는 서버리스 애플리케이션을 위한 모니터링과 트러블 슈팅 솔루션이다.

→ cpu 시간, 메모리 디스크, 네트워크의 정보를 포함한 시스템 수준의 지표를 수집, 집계, 요약한다.

람다 함수 옆에서 실행되며 lambda insight라는 대시보드가 생성된다.

세부 모니터링이 필요할 때 사용된다.

### cloudwatch contributor(기고자) insight

기고자 데이터를 표시하는 시계열 데이터를 생성하고 로그를 분석한다.

이 서비스는 네트워크의 상위 대화자를 찾고 시스템 성능에 미치는 대상을 파악할 수 있다.

vpc log, dns log, aws가 생성한 모든 로그에서 작동한다.

→ 불량 호스트를 찾아낼 수 있다.

규칙은 직접 생성하거나 aws가 생성한 간단한 규칙을 활용할 수 있다.

### cloudwatch application insight

모니터링하는 애플리케이션의 잠재적인 문제와 진행중인 문제를 분리할 수 있도록 자동화된 대시보드를 제공한다.

java, .net, 특정 디비를 선택해 선택한 기술로 애플리케이션을 실행할 수 있고 ebs, s3, 같은 aws 서비스와 연결된다.

애플리케이션에 문제가 있는 경우  이게 자동으로 대시보드를 생성하여 서비스의 잠재적인 문제드를 보여준다.

자동화된 대시보드를 생성할 때 백그라운드에서는 내부에서 sagemaker가 실행된다.

→ 트러블슈팅이나 애플리케이션을 보수하는 시간이 줄어든다.

발견된 모든 점과 알림은 eventbridge, ssm opsCenter로 전달된다.