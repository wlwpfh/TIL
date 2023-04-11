## 스트림
: 스트림은 자바 8부터 추가되어 컬렉션의 저장 요소를 하나씩 참조해서 람다식으로 처리할 수 있도록 해주는 반복자이다.  

<br/>

### 특징
: `iterator`와 비슷한 역할을 하는 반복자  
: 람다식 또는 메소드 참조를 이용해서 요소 처리 내용을 매개값으로 전달할 수 있다.  
```java
    Stream<Student> stream=list.stream();

    stream.forEach( s -> {
        String name=s.getName();
        int score=s.getScore();
    });
```
<br/>

### 종류
`BaseStream` 인터페이스를 부모로 하여 `Stream`, `IntStream`, `LongStream`, `DoubleStream`이 자식 인터페이스로 구성되어있다.   

`Stream`은 객체 요소를 처리하고 `IntStream`, `LongStream`, `DoubleStream`은 각각 `int`, `long`, `double` 요소를 처리하는 스트림이다.  

1. 컬렉션으로부터 스트림 얻기
```java
    List<Student> studentList=Arrays.asList{
        ...
    };

    Stream<Student> stream= studentList.stream();
```

<br/>

2. 배열로부터 스트림 얻기
```java
    String[] strArray= { ... };

    Stream<String> stream=Arrays.stream(strArray);
```

<br/>

3. 숫자 범위로부터 스트림 얻기  
```java
    int sum=0;
    IntStream stream=IntStream.rangeClosed(1, 100);

    stream.forEach( a -> sum+=a);
```

<br/>

4. 파일로부터 스트림 얻기
```java
    Path path= Paths.get("...");

    Stream<String> stream=Files.lines(path, Charset.defaultCharset());
```

<br/>

5. 디렉토리로부터 스트림 얻기
```java
    Path path= Paths.get("...");

    Stream<String> stream=Files.list(path);

    stream.forEach( p -> {  ... });
```