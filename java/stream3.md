## Stream 처리 메소드

### 필터링 - distinct, filter
요소를 걸러내는 역할을 한다.  
`distinct()`는 중복을 제거한다. Stream의 경우 `Object.equals(Object)`가 `true`이면 동일한 객체로 판단하고 중복을 제거한다.  
`IntStream, DoubleStream, LongStream`의 경우 동일값일 때 중복을 제거한다.  

<br/>

`filter()`는 매개값으로 주어진 predicate가 true를 리턴하는 요소만 필터링한다.  

```java
    names.stream()
        .distinct()
        .forEach(n -> System.out.println(n));

    names.stream()
        .filter( n-> n.startsWith("신"))
        .forEach( n-> System.out.println(n));
```

<br/>

### 매핑 - flatMap, map, asXXXStream, boxed
`flatMap`은 요소를 대체하는 복수 개의 요소들로 구성된 새로운 스트림을 리턴한다.  
```java
    List<String>inputList=Arrays.asList("a a","b b", "c c");

    inputList .stream()
        .flatMap( data -> Arrays.stream(data.split(" ")))
        .forEach( word -> System.out.println(data));

``` 
<br/>

`map`은 요소를 대체하는 요소로 구성된 새로운 스트림을 리턴한다.  
```java
    studentList.stream()
        .mapToInt(Student::getScore)
        .forEach( score -> System.out.println(score));
```
<br/>

`asDoubleStream`, `asLongStream`, `boxed`는 타입을 박싱해서 스트림을 생성한다.  
```java
    intStream
        .asDoubleStream()
        .forEach( ..);

    intStream
        .boxed()
        .forEach( ...);

```

<br/>

`sorted`는 요쇼를 정렬하여 최종 처리 순서를 변경할 수 있다.  
```java
    studentList.stream()
        .sorted()
        .forEach(...);
```
<br/>

루핑은 요소 전체를 반복하는 것을 말한다.  
`peek`, `forEach`이 있으며 peek은 중간 처리메소드이며 forEach는 최종 처리 메소드이다.  

```java
    int total=Arrays.stream(intArr)
        .filter( a -> a%2==0)
        .peek(n -> ...)
        .sum();

    Arrays.stream(intArr)
        .filter(a -> a%2==0)
        .forEach( ...);

```

<br/>

`allMatch`는 모든 요소들이 조건을 만족하는지, `anyMatch`는 최소 하나라도 조건을 만족하는지, `noneMatch`는 모든 요소들이 조건을 만족하지 않는지 조사한다.  

```java
    bool result=Arrays.stream(intArr)
        .allMatch(a -> a%2==0);

    result=Arrays.stream(intArr)
        .anyMatch(a -> a%2==0);

    result=Arrays.stream(intArr)
        .noneMatch(a -> a%2==0);
```

<br/>

집계는 최종 처리 기능으로 요소들을 처리해서 하나의 값으로 산출하는 것을 의미한다.    
집계는 대량의 데이터를 가공해서 축소하는 리덕션(`Reduction`)이라고 볼 수 있다.  
