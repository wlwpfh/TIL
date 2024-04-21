## stateful web app: myclothes.com  

**만들 사이트의 요건**

- 해당 사이트에는 장바구니가 있다. → 사용자들은 장바구니를 잃어버리면 안된다.
- 수백명의 사용자가 동시에 있다.
- 확장할 수 있어야 하며 수평 확장성을 유지하며 애플리케이션을 최대한 무상태로 유지하고 싶다.
- DB에 주소 등 사용자 정보를 효과적으로 보관해야 한다.

[Deployment of 3 tier application on AWS cloud](https://medium.com/@nikira8086/deployment-of-3-tier-application-on-aws-cloud-32b7eb14210a)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/d43789a8-22eb-47f3-9d18-b84f77094908/Untitled.png)

애플리케이션이 ELB에 접근하고 ELB는 인스턴스과 연결해준다.

새로 생긴 요청이 발생하면 처음 연결된 인스턴스가 아닌 다른 인스턴스와 연결된다.

⇒ 만들었던 장바구니가 사라진다!

### 1. Introduce Stickiness (Session Affinity)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/e0b82ba2-819a-4bea-bd34-a3c1cf0c40b9/Untitled.png)

세션 밀접성을 도입할 수 있다.

ELB의 기능으로 ELB Stickiness를 활성화한다.

사용자가 첫번째 인스턴스에 접속하여 장바구니에 추가하면 고착도 때문에 다음번의 요청들은 다 첫번째 인스턴스로 향하게 된다.

하지만 만약 인스턴스가 종료된다면 장바구니를 잃어버리게 된다.

### 2. Introduce User Cookies

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/fba205df-3038-437f-8bc9-cc6e80de4e9d/Untitled.png)

인스턴스가 장바구니 내용을 저장하는 대신에 상요자쪽에서 장바구니 내용을 저장하도록 하는 것이다.

로드밸런서에 접속할 때마다 장바구니에 뭐가 있는지 말하게 되는 것이다.

⇒ 이는 웹 쿠키를 통해 이뤄진다.

어느 서버에 접속해도 사용자가 직접 인스턴스로 장바구니 내용을 보여주기 때문에 각 서버는 장바구니의 내용을 알 수 있다.

서버가 이전에 있었던 일을 알 필요가 없는 무상태(stateless)를 달성했다.

하지만 HTTP 요청이 점점 더 무거워지는 문제가 있다.

그리고 공격자에 의해 쿠키가 수정될 수 있어 보안 위험도 존재한다.

그렇기에 인스턴스가 반드시 사용자 쿠키의 내용을 검증해야 한다.

그리고 쿠키의 크기는 최대 4KB이하까지만 가능해 아주 작은 정보만 저장할 수 있다.

### 3. Introduce Server Session

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/9a31c83c-ba34-48f2-af1e-0c773e4e2840/Untitled.png)

쿠키가 아닌 세션 ID만 보내는 것이다.

⇒ 사용자에 대한 세션 ID

이걸 보내면 뒤에서는 ElastiCache 클러스터가 존재한다.

세션 ID를 보낼 때 인스턴스에게 이 물건을 장바구니에 추가할거라고 말한다.

그러면 인스턴스는 장바구니 내용을 ElastiCache에 추가하고 장바구니 내용을 불러올 수 있는 ID가 세션 ID가 된다.

사용자가 세션ID와 함께 두번째 요청을 보내면 이는 다른 인스턴스로 가게 되고 해당 인스턴스는 세션 ID를 사용하여 ElastiCache로부터 장바구니 내용을 찾아 세션 데이터를 불러올 수 있다.

ElastiCache의 장점은 1천분의 1초 이하의 성능을 가진다는 점이다.

세션 데이터 저장의 또 다른 방식으로 DynamoDB가 있다.

### 4. Storing User Data in a Database

이번에는 RDS 인스턴스와 통신할 것이다.

RDS는 장기적인 저장을 위한 것이라 좋다.

RDS와 직접 통신함으로써 주소, 이름 등의 정보를 저장하거나 불러올 수 있다.

그리고 인스턴스가 RDS와 통신할 수 있으며 일종의 다중 AZ 무상태 솔루션을 효과적으로 얻을 수 있다.

사용자들의 읽기 행위가 많아지면 읽기를 어떻게 확장할까?

→ 쓰기를 수행하는 RDS 마스터를 사용할 수 있다.

복제가 일어나는 동안 RDS 읽기 전용 복제본을 사용할 수도 있다.

### 5. Scaling Reads (Alternative) - Write Through

캐시를 이용하는 쓰기 모드도 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/32b60a49-4c47-41f2-a4c4-71cf24653a4f/Untitled.png)

해당 정보를 가지고 있는지 캐시에 물어본 다음에 없다면 RDS에 물어봐서 elastiCache에 넣어둔다.

캐시에 있으면 캐시 히트가 된다.

→ 즉시 응답을 받는다.

→ RDS의 성능을 향상시키고 트래픽을 줄일 수 있다.

### 6. Multi AZ - Survive Distaters

사용자가 route 53과 통신을 하며 다중 AZ ELB가 있다.

모든 기능이 Multi AZ 기능이 있다.

RDS에서 재해 발생을 대비한 대기 복제본이 있다.

레디스를 사용한다면 ElastiCache도 다중 AZ 기능을 가지고 있다.

### 7. Security Groups

보안 그룹에 대해서는 매우 안전해야 한다.

ELB쪽 어디서나 HTTP, HTTPS 트래픽을 열 수 있다.

EC2 인스턴스는 로드 밸런서로부터 오는 트래픽만 제한한다.

ElastiCache, RDS는 EC2 보안 그룹으로부터 오는 트래픽만 제한한다.