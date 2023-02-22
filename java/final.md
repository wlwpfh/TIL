## * Final 필드와 상수
### * final 필드
- 초기값이 저장되면 이것이 최종적인 값이 되어 프로그램 실행 도중에 수정할 수 없다.  
- 초기값을 주는 방법은 두 가지로 필드 선언 시에 주는 방법과 생성자에서 주는 방법이 있다.
- 만약 초기화되지 않은 final 필드를 그대로 남겨두면 컴파일 에러가 발생한다. 
- 객체마다 저장되고, 생성자의 매개값을 통해 여러 가지 값을 가질 수 있다. 

### * 상수
- 불변의 값을 가진다.
- static이면서 final이다.
- static final 필드는 객체마다 저장되지 않고 클래스에만 포함된다. 한번 초기값이 저장되면 변경할 수 없다.
``` java 
static final 타입 상수;
static {
    상수 = 초기값;
}
```

<br/>

```java
    static final double EARTH_RADIUS=6400;
    static final double EARTH_SURFACE_AREA;

    static{
        EARTH_EARTH_SURFACE_AREA= 4 * Math.PI * EARTH_RADIUS * EARTH_RADIUS;
    }
```