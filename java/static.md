## * static
: 정적 멤버는 클래스에 고정된 멤버로 객체를 생성하지 않고 사용할 수 있는 필드와 메소드를 말한다.

### * static 선언 방법
```java
    public class Class{
        static int value;
        static void method(){ ... }
    }
```
: 정적 필드와 정적 메소드는 클래스에 고정된 멤버이므로 클래스 로더가 클래스를 로딩해서 메소드 메모리 영역에 적재할 때 클래스별로 관리된다. 
<br/>
: 주로 객체마다 가지고 있을 필요성이 없는 공용적인 데이터를 정적 필드로 선언하는 것이 좋다.  
: 정적 메소드의 경우 인스턴스 필드를 이용하지 않는다면 정적 메소드로 선언한다.    
<br/>

### * static 사용 방법
:  클래스.필드, 클래스.메소드명()으로 접근한다.
```java
    public class Calculator{
        static double pi=3.14;
        static int plus(int x, int y){ ... }
        static int minus(int x, int y){ ... }
    }
```
```java
    double result1=10*10*Calculator.pi;
    int result2=Calculator.plus(10,5);
    int result3=Calculator.minus(10,5);

```
: 클래스 이름으로 접근해야 하지만 객체 참조 변수로도 접근이 가능하다.
```java
    Calculator myCal=new Calculator();
    double result1=10*10*myCal.pi;
    int result2=myCal.plus(10,5);
    int reuslt3=myCal.minus(10,5);
```   
<br/>
### * 정적 초기화 블록
: 정적 필드는 필드 선언과 동시에 초기값을 주는 것이 보통이다.

``` java 
    static double pi=3.14;
```
: 정적 필드의 초기화 작업을 위해 정적 블록을 사용한다.
```java
    static{
        ...
    }
```
정적 블록은 클래스가 메모릭로 로딩될 때 자동으로 실행된다. 

### * 주의할 점
: 객체가 없어도 실행될 수 있기에 인스턴스 필드나 인스턴스 메소드를 사용할 수 없다. 