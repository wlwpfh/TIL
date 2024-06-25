## iam 조건과 정책의 의미

iam 조건은 iam 내부의 정책에 적용되며 사용자 정책일 수 있고 다른 정책일 수 있다.

1. aws:SourceIp

api 호출이 생성되는 클라이언트 ip를 제한하는데 사용되는 조건이다.

모든 작업과 리소스를 deny하지만 조건이 있다.

1. aws:RequestedRegion

글로벌 조건이며 api 호출 리전을 제한한다.

특정 리전에서 특정 서비스에 대한 액세스를 거부한다.

1. ec2:ResourceTag

ec2 인스턴스의 태그에 적용된다.

ec2 인스턴스가 올바른 태그를 갖고 있으면 허용된다.

1. aws:MultiFactorAuthPresent

멀티팩터 인증을 강제한다.

### s3 버킷에 대한 iam 정책

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/5db0be48-2402-4b8f-bb33-88dd48bfdaae/Untitled.png)

1. 버킷 목록 s3:ListBucket

s3:::test라는 arn에 적용된다.

버킷 수준의 권한이므로 버킷을 특정해야 한다.

1. s3:GetObject, s3:PutObject, s3:DeleteObject

버킷 내의 객체에 적용되므로 arn가 달라진다.

버킷 내의 모든객체를 나타내는 /* 를 붙인다.

### aws:PrincipalOrgId & 리소스 정책

aws:PrincipalOrgID라는 조건은 aws 조직의 멤버 계정에만 리소스 정책이 적용되도록 제한한다.

---

### iam 역할과 리소스 기반의 정책의 차이점

- 교차 계정의 경우
    1. 리소스에 리소스 기반 정책을 추가할 수 있다. → 리소스 기반 정책
    2. 리소스에 액세스할 수 있는 역할을 사용할 수 있다. → iam 역할

  ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/600d6633-106a-4609-b5e2-1bcf7627a66e/Untitled.png)


1. 역할을 맡으면 기존의 권한을 모두 포기하고 해당 역할에 할당된 권한을 상속하게 된다. → iam 정책
2. 리소스 기반 정책을 사용하면 본인이 역할을 맡지 않으므로 권한을 포기하지 않아도 된다.

   이거를 지원하는 서비스와 리소스가 많이 늘어나고 있다.


### 이 둘의 차이는 amazon eventbridge에서 사용할 때 크게 드러난다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/c404e8be-4ed8-4783-b330-2569a6c77a9b/Untitled.png)

amazon eventbridge에는 원한느 작업을 하려면 대상에 대한 권한을 요구하는 규칙이 있다.

- 리소스 기반 정책을 지원하는 대상: lambda, sns, sqs, cloudwatch logs, api gateway 등

→ 이 경우 대상 리소스를 변경해야 한다. 그래서 eventbrdige가 원하는 작업을 수행할 수 있도록 허용해야 한다.

- iam role: kinesis data streams, system manager run command, ecs task 등

→ iam 규칙을 eventbridge 규칙에 추가하면 kinesispㅇ 쓰기 작업을 수행할 권한을 갖게 된다.

### iam 권한 경계 (permission boundaries)

권한 경계 = 사용자와 역할만 지원하고 그룹은 지원하지 않는다.

iam 개체의 최대 권한을 정의하는 고급 기능이다.

이를 iam 사용자에 연결하면 권한 경계를 설정하는 것이다.

추가적으로 iam 정책을 통해 iam 권한을 지정해야 한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/09c1c256-0436-41ad-a896-5b89a97d7c18/Untitled.png)

→ 권한 경계와 iam 정책을 통한 권한이 완성된다.

⇒ iam 정책은 iam 권한 경계 밖에 있기 때문에 사용자가 다른 iam 사용자를 생성할 수 없다.

iam 권한 경계는 aws organizations SCP와 함께 사용될 수 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0e50c9f9-d6b9-4369-b0d8-a40d14ac1451/86c8b3c1-ac85-4b71-b887-80be8c4ae2f1/Untitled.png)

가운데의 유효 권한이 사용자나 그룹에 부여된 자격 증명 기반 정책과 그룹이 아닌 사용자나 역할에만 적용되는 권한 경계와 계정상 모든 iam 개체에 적용되는 organizations scp 중앙에 있다.

- 사용 사례
1. 관리자가 아닌 사용자에 책임을 위임하기 위해 권한 경계 내에서 새 iam 사용자를 생성한다.
2. 개발자가 권한을 스스로 부여하고 관리하는데 스스로를 관리자로 만들지 못하게 한다.
3. 계정에 scp를 적용해서 계정의 모든 사용자를 제한하지 않고 aws organizations의 특정 사용자만 제한할 수 있다.

iam 정책은 특정 iam 액션을 할 때마다 모든 방면에서 평가된다.