### DNS Domain Name Service

→ 호스트 이름을 대상 서버 IP 주소로 번역해준다.

(URL과 호스트 이름을 IP로 변환하는 것을 이해할 수 있는 방법이다.)

도메인 이름의 계층

.com

example.com

www.example.com

api.exmaple.com

### DNS 용어

- Domain Registrar: 도메인 이름을 등록하는 곳이다.
- DNS Records: 여러 종류가 있다. A, AAAA, CNAME
- Zone file: 모든 DNS 레코드를 포함한다. → 호스트 이름과 IP 또는 주소를 일치시키는 방법
- Name Server: DNS 쿼리를 실제로 해결하는 서버이다.
- TLD (Top Level Domain): 최상위 도메인으로 .com, .us, .in, .gov 등이 있다.
- SLD(Second Level Domain): 2단계 도메인으로 amazon.com, google.com
- FQDN: Fully Qualified Domain Name

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/91ce324e-644b-44eb-89c9-d09b29b581ee/Untitled.png)

### DNS 동작 방식

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/ccf3df4d-dfd1-4a26-8940-bd8582aef0f6/Untitled.png)

---

## Amazon Route 53

- 고가용성, 확장성을 갖춘, 완전히 관리되며 권한있는 DNS이다.

(+ 권한이 있다 = 고객이 DNS 레코드를 업데이트할 수 있다는 의미이다.)

- DNS Registra이다.
- route 53의 리소스 관련 상태 확인을 확인할 수 있다.
- 1000% SLA 가용성을 제공하는 유일한 AWS 서비스이다.

### Route 53 - Record

route 53에서 여러 DNS 레코드를 정의하고 레코드르 통해 특정 도메인으로 라우팅하는 방법을 정의한다.

각 레코드 안에는

1.  도메인이나 example.com과 같은 서브도메인 이름과 같은 정보를 포함한다.
2. A, AAAA과 같은 레코드 타입을 포함한다.
3. 레코드 값 ex) 123.456.789.123
4. 라우팅 정책 - route 53이 쿼리에 응답하는 방식이다.
5. TTL - DNS 리졸버에서 레코드가 캐싱되는 시간이다.

route 53에서 지원하는 DNS 레코드 종류는 많다

→ A, AAAA, CNAME, NS

→ +) CAA, DS, MX, NAPTR, PTR, SOA, TXT, SPF, SRV

### Route 53 - Record Type

1. A - 호스팅 이름과 IPv4 IP를 매핑한다.
2. AAAA -  호스트 이름을 IPv6 주소에 매핑한다.
3. CNAME - 호스트 이름을 다른 호스트 이름과 매핑한다.

   → 대상 호스트 이름은 A, AAAA 레코드가 될 수 있다.

   → route 53에서 DNS 이름 공간 / zone apex의 상위 노드에 대한 CNAMES를 생성할 수 없다.

4. NS - 호스팅 존의 이름 서버이다. 서버의 DNS 이름 또는 IP 주소로 호스팅 존에 대한 DNS 쿼리에 응답할 수 있다.

   → 트래픽이 도메인으로 라우팅되는 방식을 제어한다.


### Route 53 - Hosted Zones

호스팅 존 ⇒ 레코드의 컨테이너로 도메인과 서브 도메인으로 가는 트래픽의 라우팅 방식을 정의한다.

- **종류**
1. public hosted zone

퍼블릭 도메인 이름을 살 때 마다, 퍼블릭 호스팅 존을 만들 수 있다.

퍼블릭 존은 쿼리에 해당 도메인 이름의 IP가 무엇인지 알 수 있다.

공개된 클라이언트로부터 온 쿼리에 응답할 수 있다.

1. private hosted zone

공개되지 않은 도메인 이름을 지원한다.

가상 프라이빗 클라우드(VPC)만이 URL을 리졸브할 수 있다.

프라이빗 DNS 레코드가 있다.

### Route 53 - Records TTL (Time To Live)

myapp.example.com에서 DNS 요청을 보내면 DNS로부터 회신을 받는다.

회신 내용으로는 A레코드와 IP주소, TTL이 있으며 TTL은 300초 정도 된다고 한다.

TTL은 클라이언트에 이 결과를 캐시하도록 요청한다.

→ 300초동안 클라이언트는 결과를 캐시한다.

⇒ 클라이언트가 재요청을 보내거나 같은 호스트 이름으로 접속할 경우 클라이언트는 DNS 시스템에게 쿼리를 보내지 않아도 된다란 뜻이다.

(하지만 캐시에도 시간이 소요되어 캐시 TTL이 발생한다)

이미 저장된 답변을 이용해서 웹 서버에 접속이 가능하며 HTTP 요청 및 회신을 보낼 수 있다.

**너무 높은 TTL의 경우**

- route 53에 적은 트래픽을 준다.
- 하지만 오래된 레코드를 받을 가능성이 크다.

**너무 짧은 TTL의 경우**

- DNS에는 트래픽 양이 많아져 비용이 많이 든다.
- 오래된 레코드의 보관 시간은 짧아진다

  → 레코드 변경이 빨라진다.

- 레코드 변경 전반이 더욱 편리해진다.

TTL은 모든 레코드에 있어 필수적이다.

(별칭 레코드에서는 제외)

### CNAME vs Alias

AWS의 리소스를 사용하는 경우 호스트 이름이 노출된다.

→ 보유한 도메인에 호스트 이름을 매핑하고자할 수 있다.

1. CNAME Record

호스트 이름이 다른 호스트 이름으로 향하도록 할 수 있다.

( [app.asdf.com](http://app.asdf.com) → sdfasd.asdfsdf.com)

**루트 도메인 이름이 아닌 경우에만 가능하다.**

1. Alias Record

호스트 이름이 특정 AWS 리소스로 향하도록 할 수 있다.

**루트 및 비루트 도메인 모두에 작동한다.**

무료

자체적으로 상태 확인이 가능하다

### Route 53 - Alias Records

AWS 리소스에만 매핑이 되어 있다.

설정한 ALB IP가 바뀌면 별칭 레코드는 이걸 바로 인식한다.

Zone Apex라는 DNS 네임스페이스의 상위 노드로 사용될 수 있다.

AWS 리소스를 위한 별칭 레코드의 타입은 항상 A, AAAA이다. (IPv4/IPv6)

별칭 레코드를 사용하면 TTL을 설정할 수 없다.

### Route 53 - Alias Records Target 별칭 레코드의 대상

- ELB
- CloudFront 배포도 가능하다.
- API Gateway
- Elastic Beanstalk 환경
- S3 websites (S3 Buckets)
- VPC interface endpoint
- global accelerator accelerator
- 동일 호스트 존의 route 53
- **EC2의 DNS 이름에 대해서는 별칭 레코드를 설정할 수 없다.**