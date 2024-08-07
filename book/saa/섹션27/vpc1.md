## CIDR

클래스 없는 도메인 간 라우팅 - IP 주소를 할당하는 방법

+) 단순한 IP 범위를 정의하는데 도움을 준다.

ex) 12.23.32.34**/32 → IP 1개를 나타낸다.**

0.0.0.0/0 → 모든 IP를 나타낸다.

### CIDR의 작동 방법

- 구성 요소

기본 IP - 범위에 포함된 IP

서브넷 마스크 - IP에서 변경 가능한 비트의 개수를 정의한다.

ex) /0, /24, /32

ex) /8 = 255.0.0.0

ex) /16 = 255.255.0.0

- 서브넷 마스크 더 알아보기

IP 주소 /32는 2의 0승으로 IP 하나만 허용한다.

192.168.0.0/32 ⇒ 192.168.0.0

IP 주소 /31은 2의 1승으로 두 개의 IP를 허용한다.

192.168.0.0/32 ⇒ 192.168.0.0, 192.168.0.1

IP 주소 /30은 2의 2승으로 네 개의 IP를 허용한다.

IP 주소 /29은 2의 3승으로 8개의 IP를 허용한다.

…

IP 주소 /24은 2의 8승으로 256개의 IP를 허용한다.

..

IP 주소 /16은 2의 16승으로 65536개의 IP를 허용한다.

…

IP는 옥텟 4개로 구성된다.

/32 → 옥텟 변경 불가

/24 → 마지막 옥텟 변경 가능

/16 → 마지막 옥텟 두 개가 변경되고 다른 값을 가진다.

/8 → 마지막 옥텟 세 개가 변경되고

/0 → 모든 옥텟 변경 가능

### 공용(public) IP vs 사설(private) IP in IPv4

IANA에서 구축한 특정 IPv4 주소 블락은 사설 LAN 네트워크(= 로컬 네트워크)나 공용 인터넷 주소이다.

사설 IP는 특정 값만 허용한다.

10.0.0.0 - 10.255.255.255 ← 대형 네트워크

172.16.0.0 - 172.31.255.255 ← 또 다른 사설 IP 주소 세트로 계정을 생성할 때 aws에서 제공하는 기본 vpc가 이 네트워킹 공간에 포함된다.

전세계에 있는 IP주소는 모두 인터넷에 있는 공용 IP 주소이다.

## 기본 VPC 개요

새로운 aws 계정은 모두 기본 VPC가 있고 바로 사용할 수 있다.

새로운 EC2 인스턴스는 서브넷을 지정하지 않으면 기본 VPC에 실행된다.

기본 VPC는 처음부터 인터넷에 연결되어 있어 인스턴스가 인터넷에 접근하고 내부의 EC2 인스턴스는 공용 IPv4 주소를 얻는다.

EC2 인스턴스를 위한 공용 및 사설 IPv4 DNS 이름도 얻는다.

### VPC in AWS - IPv4

VPC는 virtual private cloud를 줄인 말이다.

단일 aws 리전에 여러 VPC를 둘 수 있다. (리전 당 최대 5개 가능)

VPC 마다 할당된 CIDR는 5개이다.

각 CIDR의 최소 크기는 /28로 16개의 주소이이다.

최대 크기는 /16으로 65536개이다.

VPC가 private 리소스이기 때문에 사설 IPv4만 가능하다

범위 종류: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

범위 선택 방법: VPC CIDR가 다른 VPC나 네트워크, 기업 네트워크와 겹치지 않게 주의해야 한다.

### 서브넷 개요

VPC 내부에 있는 IPv4 주소의 부분 범위이다.

→ 이 범위 안에서 aws가 IP 주소 5개를 예약한다.

(IP 주소 처음 4개, 마지막 1개를 서브넷마다 예약한다.)

이런 IP 주소는 사용도 안되고 ec2 인스턴스에 IP할당도 하지 못한다.

ex) CIDR 블록으로 10.0.0.0/24가 있다면

10.0.0.0 - 네트워크 주소

10.0.0.1 - aws는 VPC 라우터용으로 예약한다.

10.0.0.2 - amazon 제공 DNS로 매핑한다.

10.0.0.3 - 미래를 위해 예약하는 용이다.

10.0.0.255 - 네트워크 브로드캐스트 주소인 .255는 aws의 VPC에서는 브로드캐스트를 지원하지 않아 예약은 되지만 사용하는 것은 안된다.

ex) 만약 ec2 인스턴스에서 29개의 IP 주소가 필요하다면 ? /27은 고르면 안된다.

왜냐하면 27은 32개의 IP주소를 가지는데 그 중에 5개를 제외하면 27<29 이니까

→ 서브넷 크기는 /26이어야 한다.

## 서브넷에 인터넷 액세스를 연결하는 방법 - IGW(internet gateway)

IGW는 VPC의 리소스를 인터넷에 연결하도록 허용하는 EC2 인스턴스나 람다 함수 등이다.

수평으로 확장되고 가용성과 중복성이 높다.

VPC와는 별개로 생성해야 한다.

VPC는 IGW 하나에만 연결되며 반대도 그렇다.

IGW 자체는 인터넷 접근을 허용하지 않는다.

작동을 위해서는 라우팅 테이블을 수정해야 한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/90fe56b6-5364-477b-83a8-31cce55ffafd/Untitled.png)

## Bastion 호스트

사용자가 프라이빗 서브넷에 없는 EC2 인스턴스에 접근하고자 한다.

사용자인 우리는 퍼블릭 인터넷에 있는데 프라이빗 서브넷에 대한 인터넷 액세스는 없다.

⇒ 배스천 호스트를 사용할 수 있다.

- 배스천 호스트

이름 그대로의 EC2 인스턴스로 이 인스턴스는 퍼블릭 서브넷에 있다.

배스천 호스트 보안 그룹이라는 자체 보안 그룹안에 있다.

(추가로 프라이빗 서브넷에 있는 인스턴스에도 보안 그룹이 있다.)

→ 배스천 호수트가 프라이빗 서브넷에 있는 EC2 인스턴스에 액세스 할 수 있다.

⇒ 이 모든 것이 VPC에 존재하기 때문이다.

프라이빗 서브넷에 있는 EC2 인스턴스에 액세스하려면

1. SSH를 배스천 호스트에 연결한다.
2. 배스천 호스트가 다시 SSH를 프라이빗 서브넷의 EC2 인스턴스에 연결해야 한다.

배스천 호스트를 통해 프라이빗 EC2 인스턴스에 SSH로 액세스할 수 있다.

→ 여기서 배스천 호스트는 무조건 퍼블릭 서브넷에 있어야 한다.

배스천 호스트를 위해서는 보안 그룹이 반드시 인터넷 액세스를 허용해야 한다.

+) 하지만 모든 인터넷 액세스를 허용하면 보안상 안되기에 기업의 퍼블릭 CIDR 액세스만 허용하거나 사용자의 인터넷 액세스만 허용하는 등 제한하는 것이 좋다.

배스천 호스트의 EC2 보안 그룹을 최대한 제한하여 특정 IP만 액세스가 가능하도록 설정할 수 있다.

← 이렇게 하지 않으면 배스천 호스트에 공격자가 액세스하여 인스턴스를 공격하는 등 보안상 위험이 발생할 수 있다.

프라이빗 서브넷의 인스턴스 보안 그룹에서는 반드시 SSH 액세스를 허용해야 한다.

## NAT 인스턴스 (구형)

NAT = network address translation

NAT 인스터스는 사설 서브넷 EC2 인스턴스가 인터넷에 연결되도록 허용한다.

NAT 인스턴스는 공용 서브넷에서 실행되어야 하고, 공용 및 사설 서브넷을 연결한다.

비활성화해야하는 속성: 소스/목적지 확인 (source/destination check)

NAT 인스턴스에는 고정된 탄력적 IP가 연결되어 있어야 한다.

사설 서브넷에서 서버로 연결하는 방법은?

→ 공용 서브넷에 고유의 보안 그룹이 있는 NAT 인스턴스를 실행하면 된다.

→ 그 다음에 NAT 인스턴스에 탄력적 IP를 연결한다.

→ 라우팅 테이블을 수정하여 사설 서브넷과 공용 서브넷의 두 서브넷에 있는 EC2 인스턴스로부터 NAT 인스턴스로 트래픽을 전송하도록 한다.

→ 인스턴스의 IP는 공용 서버로 액세스하는데 NAT 인스턴스를 통과해야 한다.

→ 라우팅 테이블이 해당 서버로 트래픽을 보낸다.

⇒ 네트워크 패킷의 재작성이 이뤄진다!!!

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/b2fbccee-f5e0-4a08-9b67-53a9f6af63c7/Untitled.png)

일부의 IP가 재작성되고 NAT 인스턴스를 위해 소스 및 목적지 확인 설정을 비활성화해야 한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/e05a080c-7375-4e47-a783-d65be7653892/Untitled.png)

공용 서브넷에 NAT 인스턴스를 생성하고 거기에 탄력적 IP를 연결한다.

그리고 라우팅 테이블을 통해 사설 인스턴스가 NAT 인스턴스에서 인터넷 게이트웨이까지 통신하도록 한다.

### NAT 인스턴스 추가로..

NAT 인스턴스는 가용성이 높지 않고 초기화 설정을 복원할 수 없다.

→ 여러 AZ에 ASG를 생성해야 한다.

→ 복원되는 사용자 데이터 스크립트가 필요하다.

작은 인스턴스는 큰 인스턴스에 비교해서 대역폭이 더 작다.

보안그룹과 규칙을 관리해야한다.

인바운드:

사설 서브넷의 HTTP/HTTPS 트래픽을 허용한다.

홈 네트워크의 SSH를 허용한다.

아웃바운드:

인터넷으로 HTTP/HTTPS 트래픽이 나가도록 허용한다.

## NAT gateway (NATGW)

aws의 관리형 NAT 인스턴스이며 높은 대역폭을 가지고 있다.

가용성이 높으며 관리할 필요가 없다.

사용량 및 NAT gateway의 대역폭에 따라 청구된다.

특정 AZ에서 생성되고 탄력적 IP를 이어받는다.

EC2 인스턴스와 같은 서브넷에서 사용할 수 없어서 다른 서브넷에서 액세스할 때만 NAT gateway가 도움된다.

경로는 사설 서브넷 → NAT gateway → IGW로 간다.

대역폭은 초당 5GB이며 자동으로 초당 45GB까지 확장할 수 있다.

보안 그룹을 관리할 필요가 없다.

→ 어떤 포트를 활성화할지 생각할 필요가 없다!

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/1bb097ba-6534-4b55-973e-59f54b34a8dd/Untitled.png)

NAT gateway를 공용 서브넷에 두고 사설 서브넷의 루트를 수정한다.

→ EC2 인스턴스를 NAT gateway에 연결할 수 있다.

### NAT gateway의 고가용성

단일 AZ에서 복원 가능하고 AZ가 중지될 경우를 위해 다중 NAT gateway를 여러 AZ에 두면 결함 허용을 할 수 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/7604cda7-c2ff-4e2e-b187-0b27a5cc10c4/Untitled.png)

각 네트워크 트래픽은 AZ 안에 제한된다.

라우팅 테이블을 통해 AZ를 서로 연결할 필요는 없다.

→ 만약 az가 중지되면 az의 ec2 인스턴스가 접근 불가 상태가 된다.