## * JPA의 동작 원리 - Entity, EntityManager, Persistence Context

### * `Entity`

: 데이터베이스의 테이블에 대응하는 클래스로 `JPA`에서 관리한다.

- ### `Entity Manager Factory`

: `Entitiy Manager` 인스턴스를 관리하는 주체이다.

: 애플리케이션 실행 시 한 개만 만들어지며 사용자로부터 요청이 들어오면 `Entity Manager`를 생성한다. 

- ### `Entity Manager`

: 영속성 컨텍스트에 접근하며 `entity`에 대한 데이터베이스 작업을 제공한다.

: 내부적으로 데이터베이스 커넥션을 사용하여 데이터베이스에 접근한다.

- 사용되는 함수
    1. `find()` : 영속성 컨텍스트에서 엔티티를 검색하고 영속성 컨텍스트에 없을 경우 데이터베이스에서 데이터를 찾아 영속성 컨텍스트에 저장한다.
    2. `persist()` : `entity`를 영속성 컨텍스트에 저장한다.
    3. `remove()` : `entity class`를 영속성 컨텍스트에서 삭제한다.
    4. `flush()` : 영속성 컨텍스트에 저장된 내용을 데이터베이스에 적용한다. 

- ### 영속성 컨텍스트 `Persistence Context`

: `entity`를 영구 저장하는 환경으로 `entity manager`를 통해 영속성 컨텍스트에 접근한다. 

- ### 엔티티 생명주기
1. new (비영속) - new 키워드를 통해 생성된 상태로 영속성 컨텍스트와 관련없는 상태
2. managed (영속) 
    1.  엔티티가 영속성 컨텍스트에 저장된 상태로 영속성 컨텍스트에 의해 관리되는 상태
    2. 영속 상태에서 데이터베이스에 저장되지 않으며, 트랜잭션 커밋 시점에 데이터베이스에 반영
3. detached (준영속) - 영속성 컨텍스트에 엔티티가 저장되었다가 분리된 상태
4. removed (삭제) - 영속성 컨텍스트와 데이터베이스에서 삭제된 상태

<br/>

```java
Item item=new Item();
item.setItemName("상품 이름");

EntityManager em=entityManagerFactory.createEntityManager();

EntityTransaction transaction=em.getTransaction();
// 엔티티 매니저는 데이터 변경 시 데이터의 무결성을 위해 반드시 트랜잭션을 시작해야 한다.

transaction.begin();

em.persist(item);
// 생성한 상품 엔티티가 영속성 컨텍스트에 저장된 상태이다.

transaction.commit();
// 트랜잭션을 데이터베이스에 반영한다. 
// 이 때 영속성 컨텍스트에 저장된 상품 정보가 데이터베이스 INSERT되며 반영된다.

em.close(); // 엔티티 매니저 
emf.close(); //엔티티 매니저 팩토리
```