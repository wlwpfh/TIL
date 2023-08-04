## JDBC
### = Java Database Connectivity
JDBC는 자바에서 데이터베이스에 접속할 수 있도록 하는 자바 API이다.  

<br/>

### JDBC의 구조
JPA에서 사용하던 Controller - Service - Repository가 아닌, Controller - Service - Mapper - SQL로 이루어져 있다.
```java
    @Mapper
    public interface CouponMapper{
        public List<Coupon> selectUserCouponList(Member member);
}
```
위의 코드처럼 해당 인터페이스에 @Mapper 어노테이션을 붙여준다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.~.CouponMapper"> 
    <select id = "selectUserCouponList" parameterType="com.~.Member" resultType="com.~.Coupon">
        ...  
    </select>  
</mapper>  
```
해당 mapper에서 만든 함수명을 통해 그 안에 SQL문을 작성한다.

<br/>

### 그래서 JDBC의 동작 흐름은?
 1. JDBC 드라이버 로딩  
말 그대로 사용하고자 하는 JDBC 드라이버를 로딩한다. JDBC 드라이버는 DriverManager 클래스를 통해 로딩된다.
```java
    Classs.forName("com.mysql.cj.jdbc.Driver");
``` 
 2. Connection 객체 생성  
드라이버가 정상적으로 로딩되면 DriverManager를 통해 데이터베이스와 연결되는 세션인 Connection 객체를 생성한다.  
```java
    Connection conn = DriverManager.getConnection(url, user, password);
    return conn;
```
 3. Statement 객체 생성   
작성된 SQL 쿼리문을 실행하기 위한 객체로 정적 SQL 쿼리 문자열을 입력으로 가진다.  
```java
    Statement stmt = conn.createStatement();
```
 4. Query 실행   
생성된 Statement 객체를 이용하여 입력한 SQL 쿼리를 실행한다.  
```java
    ResultSet rs = stmt.executeQuery(sql);
```
 5. ResultSet 객체로부터 데이터 조회   
실행된 쿼리문에 대한 결과값인 ResultSet이 나온다.

 6. ResultSet, Statement, Connection 객체 Close  
JDBC API를 통해 사용된 객체들은 생성된 객체들을 사용한 순서의 역순으로 close한다.


<br/>

### +) Connection pool이란?
JDBC 실행 과정 중에서 생성되어야 할 Connection 객체를 미리 만들어 pool이라는 곳에 저장해두는 기법이다.      
Container 구동 시 일정 수의 Connection 객체를 생성하게 된다.     
필요 시 ConnectionPool을 요청해 제공받아 사용하며 작업이 끝나면 Connection Pool에 Connection 객치를 반납한다.    

<br/>

=> DB 접속 설정 객체를 미리 만들어 연결하여 메모리 상에 등록해두기 때문에 불필요한 작업이 사라져 클라이언트가 빠르게 DB에 접속할 수 있다.  
=> DB Connection 수를 제한할 수 있어 과도한 접속으로 인한 서버 자원 고갈을 방지할 수 있다.    
=> DB 접속 모듈을 공통화하여 DB서버의 환경이 바뀔 경우 쉬운 유지보수가 가능하다.    
=> 연결이 끝난 Connection을 재사용함으로써 새로 객체를 만드는 비용을 줄일 수 있다.    


참고 사이트: https://steady-coding.tistory.com/564, https://bioinfoblog.tistory.com/49    

<hr/>
