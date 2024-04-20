## Stateless Web App : WhatIsTheTime.com

**만들 폰사이트에 대한 설정**

- 이 사이트는 사람들에게 시간을 알려준다.
- 단순해서 데이터베이스가 필요없다.
- 작게 시작하고 다운타임을 수용할 수 있기를 원한다,
- 수직 및 수평적으로 확장할 필요가 있다.

[How to Build a Stateless Web App on AWS](https://medium.com/@achelengwanelson/how-to-build-a-stateless-web-app-on-aws-583309dcbb91)

### 1. Starting Simple

t2.micro 인스턴스에 사용자가 시간을 물어본다.

→ 서버가 시간을 알려준다.

⇒ 공용 EC2 인스턴스가 있고 고정 IP 주소를 갖도록 하고 싶다.

⇒ Elastic IP Address 설정

### 2. Scaling Vertically

사용자가 많아지면서 점점 더 많은 트래픽을 갖게 된다.

→ 부하를 처리하기 위해 t2.micro 인스턴스를 더 큰 것으로 교체한다. (= 수직적 확장)

확장하기 위해 인스턴스를 중지시키고 인스턴스 유형을 m5.large로 바꾸고 다시 인스턴스를 시작한다.

Elastic IP Address를 가지고 있어 동일한 공용 IP를 가지게 되어 사람들이 애플리케이션에 접근할 수 있다.

하지만 업그레이드하는 동안 다운타임이 발생하였으며 사용자들은 그 시간에 애플리케이션에 접근을 하지 못했다.

### 3. Scaling Horizontally

더욱 인기가 많아져서 수평적 확장을 해야 한다.

인스턴스가 추가되어 총 3개의 인스턴스가 되었으며 탄력적 IP를 제거한다.

(한 계정에서 리전마다 5개의 탄력적 IP만을 가질 수 있다.)

사용자들은 route53을 활용하게 되며 웹사이트 url은 api.whatisthetime.com이다.

TTL이 한 시간인 A레코드로 정했다.

A 레코드로 정했다 = DNS로부터 IP 리스트를 받는다는 의미이다.

**route53의 A 레코드 = IP**

아무튼 사용자들이 route53을 쿼리해서 EC2 인스턴스들의 IP 주소들을 얻게 된다.

이는 시간에 따라 변하고 route53이 업데이트될 것이어서 문제될 것이 없다.

업데이트를 하고 동기화를 유지할 것이다.

이제 사용자들은 EC2 인스턴스에 접근할 수 있고 관리할 탄력적 IPㄷ 존재하지 않는다.

**하지만 상황에 따라 인스턴스를 추가하고 제거할 수 있도록 확장하고 싶다면?**

### 4. Scaling Horizontally with a Load Balancer

이제 public이 아닌 private EC2 인스턴스들만 있으며 같은 AZ에서 실행할 것이다.

해당 인스턴스들은 총 3개의 m5.large 인스턴스이다.

로드밸런서 + 로드밸런서 안에 있는 상태확인 (= 하나의 인스턴스가 다운되거나 작동하지 않으면 사용자로부터 해당 인스턴스로 트래픽을 전송하지 않는다.)

ELB + 상태 확인을 하면 ELB는 드러나지만 사설 인스턴스들은 뒤에 숨어 있다.

보안 그룹 규칙을 통해 둘 사이의 트래픽을 제한한다.

이렇게되면 사용자들은 whatisthetime.com에 대해 쿼리하지만 로드 밸런서가 IP 주소를 지속적으로 바꾸기 때문에 이번에는 A레코드가 될 수 없다.

→ 하지만 로드 밸런서이기 때문에 별칭 레코드를 사용할 수 있다.

별칭 레코드를 사용하게 되면 route53으로부터 ELB를 가리키고 모든 것은 잘 작동할 것이다.

로드밸런서는 인스턴스로 리디렉션하고 트래픽의 균형을 잡는다.

이제 로드밸런서로 인스턴스들을 추가/제거하고 정렬할 수 있다.

또한 상태확인이 있어 사용자들에게 다운타임이 발생하지 않는다.

### 4. Scaling Horizontally, with an auto-scaling group

인스턴스들을 자동으로 추가/삭제하는 방법을 위해서..

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/5734e10f-6d83-49cb-990e-b19e9f6098bd/Untitled.png)

이번에는 auto scaling group이 인스턴스들을 관리한다.

기본적으로 auto scaling group이 요청에 따라 확장(축소도 가능)하도록 하는 것이다.

만약 지진이 발생해서(ㄷㄷ) AZ가 다운된다면? 애플리케이션이 다운된다.

→ 다중 AZ를 설정하면 된다.

### 5. Making our app multi-AZ

가용성을 높이려면 다중 AZ를 사용하는 것이 좋다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/20740059-9779-465c-9ae0-f2b9b12af391/Untitled.png)

이번에는 AZ 1~3에서 실행하며 ELB에는 3개의 AZ가 있다.

AZ가 여러개 있기에 auto scaling group 역시 다중 AZ에 걸쳐 있게 된다.

한 AZ가 다운되어도 다른 AZ들이 있어서 사용자를위한 트래픽을 처리할 수 있다.

### 6. Minimum 2 AZ ⇒ Let’s reserve capacity

각각의 AZ에는 최소 하나의 인스턴스가 실행중이다.

그러면 용량을 예약하면 어떨까?

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/8dd2b434-8b15-49b8-bf8a-e01b757441f2/Untitled.png)

기본적으로 애플리케이션의 비용을 줄이는 작업을 시작하는 것은 어떨까..?

그렇지않으면 항상 2개의 인스턴스가 실행되고 있는 것이다.

오토 스케일링 그룹의 용량을 최소화하기 위해 인스턴스를 예약함으로써 미래에 상당한 비용을 절감할 수 있다.

새로운 인스턴스가 추가되어도 이는 일시적으로 요청에 따른 것이기에 괜찮다.

조금 더 극단적인 비용 절감을 위해서는 스팟 인스턴스를 사용할 수 있다.

하지만 인스턴스들을 종료시키게 될 수도 있다.

### 마지막

EC2 인스턴스 →  public ip, private ip를 갖는 목적

탄력적 ip vs Route 53 vs load balancer

route53의 TTL 때문에 A레코드 사용X

→ 로드 밸런서와 별칭 레코드를 사용해야 한다.

인스턴스를 수동으로 관리하는 방법 vs auto scaling group

재난 상황에 대비하는 multi AZ

ELB health check

scruity group 규칙

용량 예약