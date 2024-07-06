## AWS WAF (Web application Firewall) 웹 애플리케이션 방화벽

계층 7에서 일어나는 일반적인 웹 취약점 공격으로부터 웹 애플리케이션을 보호한다.

→ 계층 7 = HTTP이기 때문에 HTTP 취약점 공격을 막아주는 것이다.

**WAF의 배포는 ALB, api gateway, cloudfront, appsync graphQL api, cognito 사용자 풀에 할 수 있다.**



### 배포한 후에는 웹 액세스 제어 목록(ACL - access control list)과 규칙을 정의해야 한다.

- 이 규칙은 IP 주소를 기반으로 필터링하는 등의 규칙이다.

  IP세트를 정의할 수 있으며 각 세트는 최대 10,000개의 IP 주소를 가질 수 있다.

- HTTP 헤더와 본문에 기반하여 필터링할 수 있다.

  URL 문자열을 조건으로 두어 SQL 주입, XSS 등의 일반적인 공격을 차단할 수 있다.

- 용량에 제약을 걸어 요청이 최대 2MB를 넘지 않게 할 수 있거나 지역 일치 조건을 두어 특정 국가를 막을 수 있다.
- 속도 기반 규칙을 설정하면 IP당 요청 수를 측정하여 디도스 공격을 막을 수 있다.

이러한 웹 ACL은  리전에만 적용되며 cloudfront는 글로벌로 정의된다.

규칙 그룹이라는 것이 있다.

= 여러 웹 ACL에 추가할 수 있는 재사용 가능한 규칙 모음이다.

### WAF 사용 사례 - 애플리케이션에 고정 IP를 사용하면서 로드 밸런서와 함께 WAF를 사용하고 싶을 때

WAF는 NLB를 지원하지 않음. (NLB는 layer 4에서만 작동한다.)

WAF는 ALB가 가능하지만 ALB는 고정 IP가 없다.

AWS global accelerator로 고정 IP를 할당받은 다음 ALB에서 WAF를 활성화하면 해결할 숭 ㅣㅆ다.

## AWS shield

디도스 공격으로부터 스스로를 보호하기 위한 서비스이다.

+) 여기서 디도스란? 분산 서비스 거부 공격이란 뜻으로 한번에 많은 요청이 여러 컴퓨터에서 유입되는 공격이다.

- 아무튼 디도스 공격을 방어하려면 AWS shield 스탠다드 서비스를 이용하면 된다.

  모든 aws 고객에게 무료로 활성화되어 있는 서비스

  syn/udp floods나 반사 공격 및 L3/L4 공격으로부터 고객을 보호해준다.

- 고급 보호가 필요한 고객을 위해서는 AWS shield 고급 서비스를 이용하면 된다.

  선택적으로 제공되는 디도스 완화 서비스

  더욱 정교한 디도스 공격을 막아주며 ec2, ELB, clodufront, global accelerator, route53을 보호한다.

  24시간 aws 디도스 지원팀이 있어 지원받을 수 있다.

  디도스 공격으로 인한 요금 상승을 방지할 수 있다.

  자동 애플리케이션 계층 디도스 완화도 제공하여 자동으로 WAF 규칙을 생성, 평가, 배포함으로써 L7 공격을 완화할 수 있다.


## AWS firewall manager

aws organization에 있는 모든 계정의 방화벽 규칙을 관리하는 서비스이다.

여러 계정의 규칙을 동시에 관리할 수 있다.

보안 규칙의 집합인 보안 정책을 설정할 수 있다.

- WAF 규칙 - ALB, api gateway, cloudfront에 적용
- aws shield advanced rules - ALB, CLB, NLB, elastic IO, cloudfront에 적용
- EC2, ALB, VPC의 ENI 리소스를 위한 보안 그룹을 표준화하는 보안 정책
- VPC 수준의 AWS network firewall
- aws route53 resolver DNS firewall

→ 정책은 리전 수준에서 생성되며 조직에 등록된 모든 계정에 적용된다

### WAF vs Firewall manager vs shield

공통점: 포괄적인 계정 보호를 위한 서비스이다.

WAF - 웹 ACL 규칙을 정의한다.

+) 리소스별 보호를 구성하는 데 좋다.

여러 계정엣서 WAF를 사용하고 WAF 구성을 가속하고 새 리소스 보호를 자동화하려면 firewall manager로 WAF 규칙을 관리하면 된다.

shield advanced는 디도스 공격으로부터 고객을 보호하고 WAF의 기능 외에도 더 많은 기능을 제공한다.

### 디도스 보호와 모범사례 알아보기

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/bb87a9f1-f565-4c88-87dc-2f0e0ddd69c2/Untitled.png)

EC2로 구성된 ASG가 있고 그 앞에 ELB가 있다.

이는 global accelerator를 통해 고정 ip로 노출하거나 cloudfront앞에 둘 수 있다.

1. BP1 - cloudfront

cloudfront는 엣지에서 사용한다.

⇒ 웹 애플리케이션 전송도 엣지 로케이션에서 일어난다.

syn flood, udp 반사 공격과 같은 디도스 일반 공격은 shield로 막을 수 있다.

1. BP1 - global accelerator

전 세계에서 엣지를 통해 애플리케이션에 접근할 수 있다.

shield와 완전히 통합된다.

cloudfront가 백엔드와 호환되지 않는 경우 디도스 공격 방어에 유용하게 쓰인다.

→ 이 때 global accelerator를 앞에 두는데 어떤 백엔드를 사용하던 cloudfront나 global accelerator로 엣지에 완전 분산이 가능하며 엣지 로케이션을 디도스 공격으로부터 보호할 수 있다.

1. BP3 - route53

엣지에 도메인 이름 변환을 글로벌로 설정한다.

DNS에도 디도스 보호 매커니즘을 적용할 수 있다.

### 디도스 완화 모범 사례

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/bb87a9f1-f565-4c88-87dc-2f0e0ddd69c2/Untitled.png)

**인프라 계층 방어는 BP1, BP3, BP6에 해당한다.**

cloudfront, global accelerator, route53, ELB는

높은 트래픽으로부터 EC2 인스턴스를 보호한다.

EC2 인스턴스에 도달하기도 전에 트래픽을 관리할 수 있다.

EC2 인스턴스에서 auto scaling 기능을 활성화하면(BP7)

ASG에 트래픽이 도달한다고 해도 자동으로 확장하여 애플리케이션에서 더 큰 로드를 수용할 수 있다.

ELB를 사용하는 경우에는 (BP6)

ELB가 여러 인스턴스간 트래픽을 자동으로 분산시킨다.

→ 인스턴스에 관리 가능한 양의 트래픽이 들어오면서 ASG가 확장하는 데에도 무리가 없다.

**애플리케이션 계층 방어는 BP1, BP2로 악성 요청을 감지 및 필터링하는 방식이 있다.**

cloudfront는 정적 컨텐츠 전송 시 엣지 로케이션에서 전송함으로써 백엔드를 보호한다.

ALB나 cloudfront에 WAF를 사용하여 요청 서명에 따라 요청을 필터링 및 차단할 수 있다.

WAF 속도 기반 규칙을 사용하면 악성 사용자의 ip를 자동으로 차단할 수 있다.

cloudfront로는 특정 지역을 차단할 수 있다.

cloudfront와 WAF는 관리형 서비스로 우리를 위해 요청을 필터링해준다.

**shield advanced(BP1, BP2, BP6)**

서비스를 활성화하면 자동으로 WAF 규칙을 생성하여 계층 7 공격을 완화한다.

악성 요청이 들어오지 못하도록 하거나 그 수를 최소화하는 식으로 ec2 인스턴스를 보호할 수 있다.

**공격 지점 줄이는 방법 (BP1, BP4, BP6)**

이 아키텍처를 보면 애플리케이션에 사용되는 백엔드 aws 리소스가 숨겨져있다.

cloudfront, api gateway, ELB를 사용하면 백엔드 리소스를 숨길 수 있다.

→ 공격자는 숨기면 모른다. 앞에 다른 게 있어서

보안 그룹과 네트워크 ACL 등을 설정하여 특정 IP의 트래픽을 필터링할 수 있다.

elastic ip도 aws shield advanced로 보호할 수 있다.

api 엔드포인트 자체도 보호할 수 있다.

→ api gateway

+) 엣지 최적화 모드를 사용할 경우 이미 글로벌로 설정되어 있다.

WAF + api gateway 조합 ⇒ 모든 HTTP 요청을 필터링할 수 있다.