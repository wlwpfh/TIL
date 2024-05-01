## Amazon FSx 개요

FSx는 AWS에서 완전 관리형 서비스로 타사 고성능 파일 시스템을 실행시킨다.

예시)

FSx for Lustre

FSx for Windows File Server

FSx for NetApp ONTAP

FSx for OpenZFS

### 1. FSx for Windows File Server

완전 관리형 windows 파일 서버 공유 드라이브이다.

windows를 사용하기 때문에 SMB 프로토콜과 windows NTFS를 지원한다.

microsoft active directory 통합을 지원하므로 사용자 보안을 추가할 수 있고 ACL로 사용자 할당량을 추가해 액세스를 제어할 수 있다.

**window 뿐만 아니라 linux EC2 인스턴스에서도 마운트할 수 있다.**

기존의 온프레미스 등 windows 파일 서버가 있는 경우 microsoft 분산 파일 시스템인 DFS 기능을 이용해서 파일 시스템을 그룹화할 수 있다.

→ 온프레미스의 window 파일 서버와 FSx for Windows File Server를 결합할 수 있다.

- 성능: 초당 수십 GB에 수백만 IOPS, 수백 PB의 데이터까지 확장할 수 있다.
- 스토리지 옵션

→ SSD: 지연 시간이 짧아야하는 워크로드를 저장할 수 있다.

(데이터베이스, 미디어 처리, 데이터 분석 등)

→ HDD: 비용이 더 싸고 넓은 스펙트럼의 워크로드를 저장할 수 있다.

(홈 디렉터리, CMS)

FSx for Windows File Server는 프라이빗 연결로 온프레미스 인프라에서 액세스할 수 있다.

고가용성 다중 AZ에 대해 FSx for Windows File Server를 구성할 수 있다.

모든 데이터는 재해 복구 목적으로 amazon s3에 매일 백업된다.

### 2. FSx for Lustre

Lustre는 원래 분산 파일 시스템으로 대형 연산에 사용되었다.

Lustre = Linux + Cluster

→ 머신러닝과 고성능 연산에 사용되었다.

- 사용사례

동영상 처리, 금융 모델링, 전자 설계 자동화 등에서 사용된다.

확장성이 매우 높다.

- 스토리지 옵션
1. SSD: 낮은 지연 시간, 워크로드가 많다, 크기가 작은 무작위 파일 작업,
2. HDD: 처리량이 많은 워크로드나 크기가 큰 시퀀스 파일 작업

S3와 무결절성(Seamless) 통합이 가능하다.

→ FSx로 s3를 파일 시스템처럼 읽어들일 수 있다.

→ FSx의 연산 출력값을 다시 s3에 쓸 수 있다.

VPN과 직접 연결을 통해 온프레미스 서버에서 사용할 수 있다.

### FSx file system Deployment options

1. 스크래치 파일 시스템

임시 스토리지로 데이터가 복제되지 않는다.

→ 기저 서버가 오작동하면 파일이 모두 사라진다..

최적화로 초과 버서트를 사용할 수 있다. (영구 파일 시스템보다 성능을 6배 높일 수 있다.)

단기 처리 데이터에 사용된다.

데이터 복제가 없어 비용을 최적화할 수 있다.

1. 영구 파일 시스템

장기 스토리지로 동일 AZ 영역안에서 데이터가 복제된다.

기저 서버가 오작동하면 몇 분 안에 해당 파일이 대체된다.

민감한 데이터의 장기 처리 및 스토리지를 들 수 있다.

### 3. FSx for NetApp ONTAP

aws의 관리형 NetApp ONTAP 파일 시스템

NFS, SMB, iSCSI 프로토콜과 호환 가능하다.

온프레미스 시스템의 ONTAP이나 NAS에서 실행 중인 워크로드를 AWS로 옮길 수 있다.

다양한 운영 체제에서 사용가능 (linux, window, macos, vmware cloud on aws, amazon workspaces & appstream 2.0, amazon ec2, ecs, eks)

스토리지는 자동으로 확장 및 축소된다.

복재와 스냅샷 기능을 지원한다.

→ 비용도 적게 든다. 데이터의 압축/데이터 중복제거 가능

지정 시간 복제 기능이 있다.

→ 새 워크로드 등을 테스트할 대 상당히 유용하다.

파일 시스템에서 신속히 복제가 가능하고 스테이징 파일 시스템을 둘 수 있다.

### 4. FSx for OpenZFS

aws의 관리형 OpenZFS 파일 시스템

여러 버전에서 NFS 프로토콜과 호환 가능하다.

ZFS에서 실행되는 워크로드를 내부적으로 AWS로 옮길 때 사용된다.

다양한 운영 체제에서 사용가능 (linux, window, macos, vmware cloud on aws, amazon workspaces & appstream 2.0, amazon ec2, ecs, eks)

스냅샷, 압축 지원, 비용이 적게 든다.

데이터 중복 제거 기능X

지정 시간 동시 복제 기능이 있어 새 워크로드 테스트 시에 유용하다.