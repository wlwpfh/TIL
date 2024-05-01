## Storage Gateway

### hybrid cloud for storage

aws에서는 하이브리드 클라우드를 권장한다.

⇒ 일부 인프라는 aws 클라우드에 있고 나머지는그대로 온프레미스에 두는 방식을 뜻한다.

그렇게 하는 이유는

1. 클라우드 마이그레이션이 오래 걸린다.
2. 보안 또는 규정 준수 요건이 있다.
3. 전략

S3는 독점 스토리지 기술로 NFS 규정 준서 파일 시스템인 EFS와 다르다.

storage gateway가 s3와 온프레미스 인프라를 이어주는 역할을 한다.

### AWS Storage Cloud Native Options

블록 스토리지 - amazon EBS, EC2 instance

파일 시스템 - amazon efs, amazon FSx

객체 수준 스토리지 - amazon s3, amazon glacier

### AWS storage gateway

= 온프레미스 데이터와 클라우드 데이터 간의 다리 역할을 한다.

- 사용 사례

재해 복구 목적 - 온프레미스 데이터를 클라우드에 백업할 수 있다.

백업과 복구 목적 - 클라우드 마이그레이션, 온프레미스에서 클라우드 간 스토리지 확장

tierd storage

on-premises cache & low-latency file access

- storage gateway 종류
1. S3 file gateway
2. FSx file gateway
3. volume gateway
4. tape gateway

### 1. S3 file gateway

s3 버킷에는 원하는 스토리지 클래스를 임의로 사용할 수 있다.

이 s3 버킷을 온프레미스 상의 애플리케이션 서버에 연결하려하는데 표쥰 네트워크 파일 시스템을 사용하고자 한다.

→ s3 file gateway를 생성하여 애플리케이션 서버가 NFS나 SMB 프로토콜을 사용하도록 한다.

→이 프로토콜을 통해 s3 파일 게이트웨이는 해당 요청을 HTTPS 요청으로 변환시켜 s3 버킷으로 보낸다.

해당 객체를 아카이브하고자 하는 경우 s3 버킷에 수명 주기 정책을 생성하여 s3 glacier로 객체를 옮겨서 아카이브되도록 한다.

⇒ s3 **file gateway로 구성한 모든 버킷은** NFS나 SMB 프로토콜을 이용해서 액세스할 수 있다.

⇒ 사용된 데이터는 식속한 액세스를 위해 file gateway에 캐시로 저장된다.

→ 최근에 사용한 파일만 파일 게이트웨이에 있다.

⇒ s3 버킷에는 여러 스토리지 클래스를 지원한다.

⇒ 수명주기 정책을 사용하면 s3 glacier로도 옮길 수 있다.

⇒ 버킷에 액세스하려면 각 파일 게이트웽이마다 IAM 역할을 생성해야 한다.

+) windows 파일 시스템 네이티브인 SMB 프로토콜을 사용하는 경우에는 사용자 인증을 위해 active directory와 통합해야 한다.

→ s3 file gateway에 사용자가 액세스할 때 인증을 거치며 s3 버킷에 액세스할 때도 인증을 거친다.

### 2. FSx file gateway

amazon FSx for Windows file server에 네이티브 액세스를 제공한다.

if) FSx for Windows file server가 amazon FSx 파일 시스템에 배포되어 있고 우리의 회사 데이터 센터에 있는 SMB 클라이언트에 액세스하려고 한다고 가정해보자.

→ gateway를 사용하면 자주 접근하는 데이터의 로컬 캐시를 확보할 수 있다.

= 중요한 파일의 로컬 캐시가 회사 데이터 센터에 쌓이고 접근 시 지연 시간을 단축시킬 수 있다.

또한 파일 게이트웨이에서 windows 네이티브인 SMB, NTFS, active directory가 호환 가능하다.

⇒ 그룹 파일 공유나 온프레미스를 연결할 홈 디렉터리로 사용할 수 있다.

### 3. volume gateway

블록 스토리지로 s3가 백업하는 iSCI 프로토콜을 사용한다.

볼륨이 EBS 스냅샷으로 저장되어 필요에 따라 온프레미스 볼륨을 복구할 수 있다.

- 종류
1. 캐시 볼륨

   : 최근 데이터 접근 시 지연 시간이 낮음

1. 저장 볼륨

   : 전체 데이터 세트가 온프레미스에 있으며 주기적 s3 백업이 따른다.

### 4. tape gateway

물리적 테이프를 사용하는 백업 시스템이 있는 회사가 백업 테이프 대신에 클라우드를 활용해 데이터를 백업할 수 있게 해준다.

가상 테이프 라이브러리(VTL)은 s3와 glacier를 이용한다.

테이프 기반 프로세스의 기존 백업 데이터를 iSCI 인터페이스를 사용하여 백업한다.

### Storage Gateway - Hardware appliance

게이트웨이는 회사 데이터 센터에 설치되어야 한다.

하지만 게이트웨이를 실행할 가상 서버가 없는 경우가 있다.

이럴 때 aws의 하드웨어를 사용할 수 있다.

온프레미스 서버가 없는 경우 사용할 수 있다. → amazon.com에서 주문 가능

구매 후 파일/볼륨/테이프 게이트웨이로 설정하면 된다.

물리적으로 장치이기에 리소스가 필요하다.

---

### AWS Transfer family 전송 제품군

s3나 EFS 안팎으로 데이터를 전송하려 할 때 s3 APIs는 사용하고 싶지 않을 때 + EFS 네트워크 파일 시스템도 사용하지 않고 FTP 프로토콜만 사용하려는 경우!

→ aws transfer family를 사용한다.

- 지원하는 프로토콜
1. aws transfer for FTP (=file transfer protocol) - 암호화x
2. aws transfer for FTPS (=file transfer protocol over SSL) - 전송 중 암호화
3. aws transfer for SFTP (=secure file transfer protocol) - 전송 중 암호화

전송 제품군은 완전 관리형 인프라이며 확장성과 안정성이 높다.

비용 측정: 시간 당 프로비저닝된 엔드 포인트 비용 + 전송 제품군 안팎으로 전송된 데이터의 GB당 요금

서비스 내에서 사용자 자격 증명을 저장 및 관리할 수 있다.

기존의 인증 시스템(=microsoft active directory, LDAP, Okta, Amazon cognito, custom)과 통합할 수 있다.

사용 사례: s3, EFS의 FTP 인터페이스를 갖기 위해서

선택적으로 route 53이라는 이름의 DNS를 사용해서 FTP 서비스에 고유의 호스트 이름을 제공할 수 있다.

---

### AWS DataSync

데이터를 동기화하며 이를 통해 대용량의 데이터를 한 곳에서 다른 곳으로 옮길 수 있다.

→ 온프레미스나 aws의 다른 클라우드로 데이터를 옮길 수 있다. 옮길 위치인 온프레미스나 연결할 다른 클라우드에 에이전트가 있어야 한다.

aws → aws로 마이그레이션을 실행할 수 있다 - 에이전트가  필요 엇다.

- 동기화 범위

데이터는 모든 s3의 glacier를 포함하여 모든 스토리지 클래스에 동기화할 수 있다.

amazon EFS로 네트워크 파일 시스템에 저장할 수 있다.

amazon FSx - 모든 운영 체제에서 사용 가능

복제 작업은 계속 이뤄지지 않고 일정을 지정하여 dataSync가 일정에 맞춰 데이터가 동기ㅗ하된다.

dataSync에는 파일 권한과 메타 데이터 저장 기능이 있다.

dataSync 에이전트는 속도 측면에서 상당히 강력하다.

### AWS DataSync NFS/SMB to AWS(S3, EFS, FSx, …)

1. 온프레미스와 dataSync가 실행되는 aws 리전이 있다.
2. 온프레미스에 NFS/SMB 서버가 있다.
3. 온프레미스에 aws dataSync 에이전트를 설치한다.
4. 에이전트를 NFS/SMB 서버에 연결시킨다.
5. 에이전트를 이용하여 암호화를 거쳐 dataSync 서비스에 연결한다.
6. 이 연결은 어느 위치로든 갈 수 있다.

동기화는 온프레미스에서 aws로 단방향으로 이뤄질 수 있다.

aws에서 다시 온프레미스로 동기화를 실행할 수도 있다.

만약 네트워크 용량이 부족하면 온프레미스에 aws snowcone을 설치하여 작업한다.

### Amazon DataSync Transfer between AWS storage services

s3, EFS, FSx를 다시 s3, EFS, FSx로 다시 동기화하려는 경우 dataSync 서비스를 사용하여 데이터 복사본을 만든다.

서로 다른 aws 스토리지 서비스 간 메타데이터 또한 유지된다.

dataSync로 거의 대부분의 데이터를 동기화시킬 수 있으나 지속적이지는 않고 일정에 따라 움직인다.

---

### Storage Comparison

1. S3

   : 객체 스토리지

   : 상당히 구체적인 api이지만 대부분의 aws와 연결할 수 있다.

1. S3 glacier

   : 해당 객체를 아카이브할 때 사용한다.

1. EBS Volume

   : 한 번에 인스턴스에 네트워크 스토리지를 연결할 때 사용

1. instance storage

   : IOPS가 높은 인스턴스에 네트워크 스토리지가 아닌 물리적 고성능 스토리지를 필요로 하는 경우 사용한다.

1. EFS

   : 인스턴스가 네트워크 파일 시스템을 필요로 하며 다중 가용 영역 간 마운트 + POSIX 파일 시스템을 사용하는 경우 사용한다.

1. FSx for Windows

   : 윈도우 서버에서 네트워크 파일 시스템을 필요로 하는 경우

1. FSx for Lustre

   : 고성능 연산 리눅스 파일 시스템이며 lustre 클라이언트와 호환 가능해야 하는 경우

1. FSx for NetApp ONTAP

   : 높은 운영 체제 호환성과 네트워크 파일 시스템이 필요할 때

1. FSx for OpenZFS

   : 관리형 ZFS 파일 시스템이 필요할 때

1. storage gateway

   : 온프레미스와 aws 간 스토리지를 연결하는 방법

1. transfer family

   : s3, EFS, FTP, FTPS, SFTP 인터페이스를 필요로 하는 경우 사용된다.

1. dataSync

   : 온프레미스 ↔ aws, aws ↔ aws 데이터를 동기화할 때 사용한다.

1. snowcone / snowball / snowmobile

   : 데이터를 옮기는 데 사용할 네트워크 용량이 없으나 물리적으로 대용량의 데이터를 옮겨야할 대

1. database

   : 데이터 저장이 가능하나 인덱스와 쿼리 작업을 필요로 하는 특수한 워크로드가 있다.