## 날짜  

오늘은 날짜 형식과 관련해서 알아보려 한다.  
프로젝트를 진행하면서 시간을 수정하였다. 
이유는 `EC2 서버 내에서 UTC 타임존으로 설정되는 이슈를 해결하기 위함`이었다.  
이 때까지 나는 시간 설정의 중요성을 몰랐다.  
오늘 한 번 왜 바꿔야 하는지 알아보자 ! ! !  

왜 `LocalDateTime`에서
해당 시간으로는 `now()`에서는 다르게 서버의 타임존은 UTC로 설정했기 때문에 이 기준의 시간이 나온다.  
그렇기에 `LocalDateTime.now()`에서 `.plusHours(9)`를 추가하여 시간을 더해야 한다.

<br/>

### 그전에 Timezone이란?
`TimeZone`은 동일한 로컬 시간을 따르는 지역을 의미한다.  보통 국가별로 고유한 타임존을 사용한다고 한다.  

### UTC란 ?
인터넷에 검색해본 바로 `UTC`란 `Coordinated Universal Time`이라고 나온다.  
현재 서울 기준 8월 10일 오후 1시 8분인데 8월 10일 오전 4시 7분으로 뜬다.  
그렇다면 9시간의 차이가 난다.  

기본적으로 서버와 데이터베이스의 타임존은 `UTC`를 사용한다고 한다.  
우리 서비스의 경우 국내 사용자들을 위해 서버 타임 존을 `Asia/Seoul`로 바꾸려고 한다.

이 경우에는 시간의 형식이 `YYYY-MM-DDT13:18:01+0000`의 모양이다.  

<br/>

### 그럼 UTC에서 KST로
`KST`는 이름부터 `Korean Standard Time`으로 한국 표준 시간을 의미한다.  
서버가 돌아가는 리눅스에서는 다양한 나라별 시간이 설정되어 있다.  

`UTC`와 다른 점은? 둘이 9시간 정도 빠르며 이 차이를 `offset`이라 표현한다.

1.리눅스에서 직접 설정하는 방법  
```shell
    cd /usr/share/zoneinfo
    ls -al
```
이 명령어를 통해 다양한 나라의 시간대를 볼 수 있다.
```shell
    ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```
위의 명령어를 통해 KST에 맞는 시간을 localtime 파일로 링크할 수 있다.  

<br/>

2. 코드에서 설정하는 방법

```java
    import javax.annotation.PostConstruct;
import java.util.TimeZone;

@Configuration
public class TimezoneConfig {
    @PostConstruct
    public void init() {
        TimeZone.setDefault(TimeZone.getTimeZone("Asia/Seoul"));
    }
}
```

<br/>

### 그렇다면 ISO 8601로! 
결국 우리 팀은 `ISO 8601`로 정착했다.  

`ISO 8601`은 날짜와 시간과 관련된 데이터 교환을 다루는 국제 표준화 기구에서 공포되었다.
기본적인 형식으로는 `YYYY-mm-ddTHH:mm:ss+09:00`의 모습이다.  

뒤에 `+09:00`의 의미는 `UTC` 대비 `+09:00`임을 알려준다.  


<br/>

### 뭐가 정답일까?
한 블로그 내용에 따르면 백엔드 개발자는 해당 시간 적용을 클라이언트 영역에 위임하는 것이 좋다고 이야기한다.  
사용자는 서버에 상관 없이 본인의 시간대를 기준으로 한 시간을 표시되어야 하기 때문이다.  
다양한 위치에서의 사용자를 고려할 때 시간에 대해서 더욱 생각해봐야겠다.  

참고: https://heekim0719.tistory.com/406, https://kdevkr.github.io/don't-change-server-timezone/
