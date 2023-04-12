## Stream Pipeline
컬렉션의 요소를 리덕션의 결과물로 바로 집계할 수 없는 경우 집계하기 좋도록 필터링, 매핑, 정렬, 그룹핑 등의 중간 처리가 필요하다.  

<br/>

### 중간 처리와 최종 처리
스트림은 데이터의 필터링, 매핑, 정렬, 그룹핑 등이 중간 처리와 합계, 평균, 카운팅, 최댓값, 최솟값 등의 최종 처리를 파이프라인으로 해결한다.  
파이프라인은 여러 개의 스트림이 연결되어 있는 구조를 말한다.  
파이프라인에서는 최종 처리를 제외하고는 모두 중간 처리 스트림이다.  

<br/> 

중간 스트림이 생성될 때 요소들이 중간처리되는 것이 아니라 최종 처리가 시작되기 전까지 중간 처리는 지연(`lazy`)된다.  
최종 처리가 시작되면 비로소 컬렉션의 요소가 하니씩 중간 스트림에서 최종 처리까지 오게 된다.  

```java
    Stream<Member> maleFemaleStream=list.stream();
    Stream<Member> maleStream=maleFemaleStream.filter(m -> m.getSex()==Member.MALE);

    IntStream ageStream=maleStream.mapToInt(Member::getAge);
    OptionalDouble optionalDouble= ageStream.average();
    double ageAvg=optionalDouble.getAsDouble();
```

```java
    double ageAvg=list.stream() // 오리지널 스트림 
        .filter(m -> .getSex()==Member.MALE) //중간처리 스트림
        .mapToInt(Member::getAge) //중간처리 스트림 
        .average() // 최종 처리 
        .getAsDouble();
```

<br/>

## 종류 
### 1. 중간처리
: 필터링 - distinct, filter  
: 매핑 - flatMap, flatMapToDouble, flatMapToInt, flatMapToLong, map, asDoubleStream, boxed, ...       
: 정렬 -sorted    
: 루핑 - peek    

<br/>

### 2. 최종처리
: 매칭 - allMatch, anyMatch, noneMatch  
: 집계 - count, findFirst, max, min, average, reduce, sum  
: 루핑 - forEach  
: 수집 - collect  

리턴 타입이 스트림이면 중간 처리 메소드, 리턴 타입이 기본이거나 OptionalXXX라면 최종 처리 메소드이다.  

