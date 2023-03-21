## * Singleton Pattern

### * 특징  
1. 싱글톤 패턴은 하나의 클래스에 하나의 인스턴스만 가지는 패턴이다.  
2. 하나의 인스턴스를 생성한 후 다른 모듈들이 해당 인스턴스를 공유하며 사용한다.  
3. 하나의 인스턴스를 생성하기에 인스턴스 생성할 때 드는 비용이 줄어든다.  
4. 하나의 인스턴스를 공유하기에 의존성이 높아진다.    

<br/>

### * 사용법

```java
public class SingletonService {

    private static final SingletonService instance= new SingletonService(); 

    public static SingletonService getInstance(){
        return instance;
    } 


    private SingletonService(){

    }

}
```
1. `SingletonService` 클래스를 만들어 `SingletonService` 객체를 생성한다.
2. `getInstance`를 통해 조회하도록 허용한다.
3. `private` 생성자를 통해 추가적인 객체 생성을 막는다. 

<br/>


### * 사용 시 단점
1. TDD에 방해가 된다.  
= TDD에 주로 사용되는 단위 테스트는 독립적이어야 하며 어떤 순서로든 실행되어야 한다. 하지만 싱글톤 패턴은 하나의 인스턴스를 기반으로 구현하기에 테스트마다 독립적인 인스턴스를 생성하기 힘들다.
2. 의존성 주입  
= 싱글톤 패턴은 모듈 간의 결합을 강하게 만든다.  
    +) 이는 의존성 주입을 통해 모듈 간의 결합을 느슨하게 만들어 해결할 수 있다.  
