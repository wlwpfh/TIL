## Controller의 신비  

말을 신비라고 했지만 `synchronized`를 공부하던 도중 읽게 된 글을 보고 엇 그러게라는 생각이 들었다.   

바로 하나의 `Controller`가 여러 개의 요청을 처리하는 부분이다.  
Controller 클래스 정보가 **JVM 메모리 영역 중에 메소드 영역**에 올라간다고 한다. 

<br/>

**+) `JVM`의 메모리 영역** 
1. `method area` - 클래스 멤버 변수의 이름, 데이터 타입, 접근 제어가와 같은 필드 정보, 메소드 이름, 리턴 타입, 파라미터, 접근 제어자와 같은 정보, static 변수 등이 해당된다.  
2. `heap area` - new 키워드로 생성된 객체와 배열이 해당된다. 
3. `stack area` - 지역변수, 파라미터, 리턴 값, 연산에 사용되는 임시 값들이 생성되는 영역이다.  
4. `PC register` - 현재 스레드가 실행되는 부분의 명령어의 주소를 저장하는 영역이다.  
5. `native method stack` - 자바 이외의 언어로 작성된 네이티브 코드를 실행할 때 사용되는 메모리 영역이다.  

- `thread`가 공유하는 공간 - `method area`, `heap area`
- 각 `thread`가 생성하는 공간 - `stack area`, `PC register`, `native method stack`

<br/>
<hr/>

```java
    @Controller
    public class UserController{
    ...
    }
```
이 클래스는 `method area`에 저장된다.  
```java
    UserController userController = new UserController();
```
이 객체는 `heap area`에 저장된다.  

**이 두 영역 다 모든 `thread`가 공유하는 공간에 저장되기 때문에 공유할 수 있다.**
**=> 모든 `thread`가 객체의 메소드를 공유할 수 있기 때문에 `Controller`는 1개만 생성된다.**  

<br/>
<hr/>

+) `Controller`는 `Singleton`이다.  
: `bean`은 `Singleton`으로 생성되고 관리된다.   
: 각각의 `thread`는 `singleton`으로 생성된 `bean`들을 참고하여 일한다.

<br/>



출처: https://jeong-pro.tistory.com/204, https://velog.io/@ejung803/Spring-Web-MVC%EC%97%90%EC%84%9C-%EC%9A%94%EC%B2%AD-%EB%A7%88%EB%8B%A4-Thread%EA%B0%80-%EC%83%9D%EC%84%B1%EB%90%98%EC%96%B4-Controller%EB%A5%BC-%ED%86%B5%ED%95%B4-%EC%9A%94%EC%B2%AD%EC%9D%84-%EC%88%98%ED%96%89%ED%95%A0%ED%85%90%EB%8D%B0-%EC%96%B4%EB%96%BB%EA%B2%8C-1%EA%B0%9C%EC%9D%98-Controller%EB%A7%8C-%EC%83%9D%EC%84%B1%EB%90%A0-%EC%88%98-%EC%9E%88%EC%9D%84%EA%B9%8C%EC%9A%94
