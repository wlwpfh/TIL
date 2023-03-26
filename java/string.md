## * String
: 문자열을 저장하는 String은 내부의 문자열을 수정할 수 없다. 

```java
    String data="ABC";
    data+="DEF";
```
`+` 연산을 통해 기존의 객체에 추가되는 것이 아닌 또 하나의 `ABCDEF`를 가진 새로운 `String` 객체가 생성된다.
`+` 연산을 많이 사용하면 그만큼의 `String` 객체의 수가 늘어나기 때문에 프로그램의 성능을 느리게 하는 요인이 된다.  
<br/>

## * StringBuffer
: 내부 버퍼에 문자열을 저장해 두고 그 안에서 추가, 수정, 삭제 작업을 할 수 있도록 설계되어 있다.  
: 멀티 스레드 환경에서 사용할 수 있도록 동기화가 적용되어 있어 스레드에 안전하다.

## * StringBuilder
: 내부 버퍼에 문자열을 저장해 두고 그 안에서 추가, 수정, 삭제 작업을 할 수 있도록 설계되어 있다.    
: 단일 스레드 환경에서만 사용하도록 설계되어 있다.  

### * 예시

```java
    StringBuilder sb=new StringBuilder();
    StringBuilder sb=new StringBuilder(16);
    StringBuilder sb=new StringBuilder("JAVA");
```
`StringBuilder`의 생성자를 통해 16개의 문자들을 저장할 수 있는 초기 버퍼를 만든다.  
`StringBuilder`의 생성자를 통해 `capacity`로 주어진 개수만큼 문자들을 저장할 수 있는 초기 버퍼를 만든다.  
`StringBuilder`의 생성자를 통해 `str`로 주어진 매개값을 버퍼의 초기값으로 저장한다.  
