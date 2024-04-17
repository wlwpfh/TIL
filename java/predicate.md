## Predicate
`Predicate`는 함수형 인터페이스로 boolean값을 리턴한다.
`filter`의 성격을 띄기에 그 성격을 네이밍에 표현한다.

```java
@FunctionalInterface
public interface Predicate<T> {
    /* 검증 */    
    boolean test(T t);
    
    /* 다른 Predicate와 연결 - && */
    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }

    /* 반대의 값을 반환 */
    default Predicate<T> negate() {
        return (t) -> !test(t);
    }
    
    /* 다른 Predicate와 연결 - || */
    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }
    
    /* 동일한지 확인 */
    static <T> Predicate<T> isEqual(Object targetRef) {
        return (null == targetRef)
                ? Objects::isNull
                : object -> targetRef.equals(object);
    }
}
```

<hr/>

### 사용 예제
```java
    public class PredicateSampleTest{
        public void sample(){
            int a = 5;
            Predicate<Integer> isBiggerThanA = num -> num > a;
            Predicate<Integer> isEqualA = num -> num == a;
            Predicate<Integer> isLessThanA = num -> num < a;
    
            List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9,10));
    
            List<Integer> equalAList = numbers.stream()
                    .filter(isBiggerThanA.and(isEqualA))
                    .collect(Collectors.toList());
            // 없음 
    
            List<Integer> belowAList  = numbers.stream()
                    .filter(isEqualA.or(isLessThanA))
                    .collect(Collectors.toList());
            // 1 2 3 4 5
        }
}
```
`filter`함수 안에서 사용할 수 있으며 `and`,`or`를 통해 다른 `Predicate`와 이어준다.
