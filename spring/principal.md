## Principal

``` 
This interface represents the abstract notion of a principal, which can be used to represent any entity, such as an individual, a corporation, and a login id.
```
현재 로그인한 객체에 대해 값을 받아 이용할 수 있는 인터페이스

<br/>

### 함수
1. `equals`: 해당 principal과 같은지 boolean 형태로 반환한다. 

2. `toString`: 해당 principal에 대해 string 형태로 반환한다. 

3. `hashCode`: 해당 principal에 대해 hashcode를 반환한다.  

4. `getName`: 해당 principal에 대해 name을 반환한다.  로그인 시 사용되었던 ID이 name이 된다.  

5. `implies`: 특정 subject에 대해 principal이 포함하고 있다면 true를 아니면 false를 반환한다.  

<br/>

### * 구성

```java
System.out.println("principal =" + principal.toString());
```
이에 대한 출력 결과   
```
UsernamePasswordAuthenticationToken [Principal=org.springframework.security.core.userdetails.User 
    [Username=0112jyoung@naver.com, Password=[PROTECTED], Enabled=true, AccountNonExpired=true, credentialsNonExpired=true, AccountNonLocked=true, Granted Authorities=[ROLE_ADMIN]], 
    Credentials=[PROTECTED],
    Authenticated=true,
    Details=WebAuthenticationDetails [RemoteIpAddress=0:0:0:0:0:0:0:1, SessionId=6B3039AAEE8672E78410AEE28XXXX],
    Granted Authorities=[ROLE_ADMIN]]

```