## * 정의
- spring에 의해서 생성되고 관리되는 자바 객체 

<br/>

## * 사용 방법
### 1. Annotation 사용
- 주석 뿐만 아니라 메타데이터로 추가적인 기능을 제공한다
- Component Annotation을 이용하여 등록할 수 있다 

### 2. Bean에 직접 등록
- Configuration 파일을 통해 ```@Bean```을 표시하여 등록하여 사용할 수 있다. 
- ```applicationContext.getBean()``` 메소드를 사용하여 찾을 수 있다. 
## * bean scope
- 빈이 생성되고 존재하고 적용되는 범위
- 빈은 싱글톤이기에 기본적으로 싱글톤 스코프를 갖는다. 