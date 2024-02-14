## 아이템3. private 생성자나 열거 타입으로 싱글턴임을 보증하라.  

**싱글턴이란?**  
: 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.  
: 함수와 같은 무상태 객체나 설계상 유일해야 하는 시스템 컴포넌트를 들 수 있다.  

### 싱글턴을 만드는 방식
**1. 첫번째 방식**  
생성자는 `private`으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 `public static` 멤버를 하나 마련해둔다.  

```java
    public class Elvis{
        public static final Elvis INSTANCE = new Elvis();
        private Elvis() {}
    
        public void leaveTheBuilding() {}
    }
```
위의 경우는 `public static final` 필드 방식의 싱글턴이다.   
`private` 생성자는 `public static final` 필드인 `Elvis.INSTANCE`를 초기화할 때 한번만 호출된다.  
`public`이나 `protected` 생성자가 없어 Elvis 클래스는 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.  

예외적인 경우로는 리플렉션 API인 `AccessibleObject.setAccessible`을 사용해 `private` 생성자를 호출할 수 있다.  
이를 방지하려면 생성자를 수정하여 두 번 째 객체가 생성되려 할 때 예외를 던지면 된다.

`Elvis.getInstance`는 항상 같은 객체의 참조를 반환하므로 또 다른 인스턴스가 만들어지지 않는다.  
이 public 필드 방식의 장점은 해당 클래스가 싱글턴임을 API에 명백히 드러난다는 것이다.  
public static 필드가 `final`이니 절대로 다른 객체를 참조할 수 없다.  
또한 간결하다.

**2. 두번째 방식**  
정적 팩터리 메서드를 `public static` 멤버로 제공한다.  
```java
    public class Elvis{
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    public static Elvis getInstance() {return INSTANCE;}
    public void leaveTheBuilding() {}
    }
```
API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 점이 장점이다.  
유일한 인스턴스를 반환하던 팩터리 메서드가 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수 있다.  
또한 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다는 점이다.  
정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다는 점이다.  
`Elvis::getInstance`를 `Supplier<Elvis>`로 사용하는 방식이다.  

**3. 세번째 방식**  
원소가 하나인 열거 타입을 선언하는 것이다.  
```java
    public enum Elvis {
        INSTANCE;
        
        public void leaveTheBuilding(){}
    } 
```
1번 방식과 비슷하지만 더 간결하고, 추가적인 노력없이 직렬화할 수 있다.  
대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.  

단, 만들려는 싱글턴이 `Enum` 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.

<hr>

싱글턴 클래스를 직렬화하려면 단순히 `Serializable`을 구현한다고 선언하는 것만으로는 부족하다.  
모든 인스턴스 필드를 일시적이라고 선언하고 `readRelove` 메서드를 제공해야 한다.  
그렇지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.  

```java
    private Object readResolve(){
        // 진짜 Elvisㄹ르 반환하고, 가짜 Elvis는 가비지 컬렉터에 맡긴다.
        return INSTANCE;
    }
```

