## * 스프링 컨테이너
- ApplicationContext를 스프링 컨테이너라 한다.
+) ApplicationContext는 interface이다. 
- ```@Configuration```이 붙은 파일을 설정 정보로 사용한다.
- 그 파일에서 ```@Bean```이 붙은 메서드를 모두 호출하여 반환된 객체를 컨테이너에 등록한다.
- 등록된 객체는 스프링 빈이다. 