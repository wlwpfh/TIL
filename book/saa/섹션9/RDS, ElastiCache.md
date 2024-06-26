### RDS & Aurora Security

RDS 및 aurora 데이터베이스에 저장된 데이터를 암호화할 수 있다.

1. 데이터가 볼륨에 암호화된다.

   → KMS를 사용해 마스터와 모든 복제본의 암호화가 이뤄진다.

   (데이터베이스를 처음 실행할 때 정의된다.)

   → 마스터 암호화를 하지 않으면, 읽기 복제본을 암호화할 수 없다.

   → 암호화되어 있지 않은 기존 데이터베이스를 암호화하려면 스냅샷을 가지고 와서 암호화된 데이터베이스 형태로 스냅샷을 복원해야 한다.

2. 클라이언트와 데이터베이스 간의 전송 중 데이터 암호화

기본적으로 전송 중 데이터 암호화 기능을 갖추고 있다.

→ 클라이언트는 AWS의 TLS 루트 인증서를 사용해야 한다.

1. IAM 인증

인증을 통해 데이터베이스에 접속할 수 있다.

1. 보안 그룹

보안 그룹을 사용해서 데이터베이스에 대한 네트워크 액세스를 통제할 수도 있다.

1. SSH Access X

→ RDS custom을 사용하면 된다.

1. 감사(?) 로그 활성화 Audit Logs

---

### Amazon RDS Proxy

vpc 내에 RDS 데이터베이스를 배포할 수 있다.

완전 관리형 RDS 데이터베이스 프록시도 배포할 수 있다.

RDS 프록시를 이용하면 장점

→ 애플리케이션이 데이터베이스 내에서 데이터베이스 연결 풀을 형성하고 공유할 수 있다.

→ RDS 프록시가 모아줘서 RDS 데이터베이스 인스턴스로 가는 연결이 줄어든다.

→ 효능: 데이터베이스 효율성 향상, 개방된 연결과 시관초과를 최소화할 수 있다.

RDS 프록시는 완전한 서버리스로 오토 스케일링이 가능해 용량을 관리할 필요가 없고 가용성이 높다.

+) 다중 AZ 지원

장애 조치가 발생하면 RDS 프록시 덕분에 RDS & Auroroa의 장애조치 시간을 66%까지 줄일 수 있다.

메인 RDS 데이터베이스 인스턴스에 애플리케이션을 모두 연결하고 장애 조치 각자 처리

→ 그 대신에 장애 처리와 무관한 RDS 프록시에 연결하는 것이다.

RDS 프록시가 장애 조치가 발생한 데이터베이스 인스턴스를 처리하여 장애 조치 시간이 개선된다.

애플리케이션의 코드를 변경하지 않아도 된다.

데이터베이스에 IAM 인증을 강제함으로 IAM 인증을 통해서만 RDS 데이터베이스 인스턴스에 연결하도록 할 수 있다.

(자격 증명 = AWS secrets manager 서비스에 저장된다.)

RDS 프록시는 퍼블릭 액세스가 완전 불가능하다. → vpc 내에서만 가능 ⇒ 보안 훌륭

지원 목록

- mysql, postgresql, mariadb RDS

---

### Amazon ElastiCache Overview

이것은 RDS가 관계형 데이터베이스를 관리하는 것과 같은 방식이다.

ElastiCache는 캐싱 기술인 redis 또는 memcached를 관리할 수 있도록 도와준다.

캐시 → 매우 높은 성능과 짧은 지연 시간을 가진 인메모리 데이터베이스이다.

읽기 집약적인 워크로드에서 데이터베이스의 로드를 줄여준다.

애플리케이션의 상태를 비저장형(stateless)으로 할 수 있게 도와준다.

AWS는 데이터베이스의 운영체제를 유지 및 관리할 수 있다.

+) 패치, 최적화, 설정, 구성, 모니터링, 장애 복구

이걸 사용하면 애플리케이션의 코드를 많이 바꿔야 한다.

→ 캐시를 쿼리하도록 애플리케이션을 변경해야 한다. → 데이터베이스를 쿼리하기 전 / 후에

### ElastiCache Solution Architecture - DB Cache

ElastiCache에 있다면 cache hit

없다면 (cache miss) RDS에서 가져와서 cache에 다시 쓰기

→ 로드를 줄이는데 도움이 된다.

캐시 무효화 전략이 필요하다.

→ 가장 최신 데이터만 사용되어야하기 때문에

### ElastiCache Solution Architecture - User Session store

애플리케이션을 상태 비저장으로 만들기 위해 사용자 세션을 저장한다.

애플리케이션으로 사용자가 로그인하면 애플리케이션은 elastiCache에 session을 쓴다.

사용자가 애플리케이션의 다른 인스턴스로 리디렉션되면, 애플리케이션은 그 세션의 세션 캐시를  amazon ElastiCache에서 직접 검색할 수 있어 사용자는 여전히 로그인 상태를 유지한다.

### ElastiCache - Redis VS Memcached

- Redis
1. 자동 장애 조치 기능이 있는 다중 AZㄱ 있다.
2. 읽기 복제본이 있다. → 고가용성
3. AOF 지속성을 이용한 데이터 내구성이 있다.
4. 백업 및 복원 기능이 있다.

⇒ 데이터 내구성에 대한 것이다. 기능 면에서는 캐시로서 세트와 정렬된 세트를 지원한다.

- memcached
1. 데이터 분할을 위해 멀티 노드를 사용한다. → 샤딩
2. 고가용성이 없고 복제가 일어나지 않으며 영구 캐시가 아니다.
3. 백업 및 복원도 없다.
4. 멀티스레드 아키텍처이다.

⇒ 분산되어 있는 순수한 캐시

### ElastiCache - Cache Security

Redis에서만 IAM 인증을 지원하며, 나머지 경우에는 사용자 이름과 비밀번호를 사용하면 된다.

elastiCache에서 IAM 정책을 정의하면 AWS API 수준 보안에만 사용된다.

Redis Auth라는 redis 내 보안을 통해 비밀번호와 토큰을 설정할 수 있다.

→ redis 클러스터를 만들 때 설정할 수 있다. (캐시에 추가 보안 수준을 제공하는 것)

→ SSL 전송 중 암호화도 지원한다.

Memcached

→ SASL 기반 승인을 제공한다.

### ElastiCache에 데이터를 로드하는 세가지 종류의 패턴

1. lazy loading

모든 데이터가 개시되고, 데이터가 캐시에서 지체될 수 있다.

1. write through

데이터베이스에 데이터가 기록될 때마다 캐시에 데이터를 추가하거나 업데이트하는 것

→ 데이터가 지체되지 않는다.

1. session store

유지 시간 기능을 사용해 세션을 만료할 수 있다.

### elastiCache - Redis use case

redis에는 정렬된 세트라는 것이 있다.

→ 고유성과 순서를 모두 보장한다.

요소가 추가될 때마다 실시간으로 순위를 매긴 다음 올바른 순서로 추가된다.

redis cluster가 있으면 실시간 리더보드를 만들 수 있다.

클라이언트가 redis를 사용해서 amazon elastiCache와 대화할 때 실시간 리더보드에 액세스할 수 있으며, 애플리케이션 측에서는 이 기능을 프로그래밍할 필요가 없다.

### 포트 번호

FTP 21

SSH 22

SFTP 22

HTTP 80

HTTPS 443

PostgreSQL 5432

MySQL & MaraiDB 3306

Oracle RDS 1521

MSSQL Server 1433

Aurora 5432 / 3306