## * transaction
- 더 이상 나눌 수 없는 단위 작업
- 원자성을 가진다.
- 만약 작업을 완료할 수 없다면 아예 작업이 시작되지 않은 것처럼 초기 상태로 돌려놔야 한다.


### * 용어
1. rollback - 문제가 발생하여 처리한 작업을 취소한다.
2. commit - 작업을 확정한다.
3. 경계 - 트랜젝션이 시작되고 끝나는 위치이다.  
    +) 하나의 Connection이 만들어지고 닫히는 범위 안에 존재한다.  

```
c.setAutoCommit(false);

try{
    ...
    c.commit();
}catch(Exception e){
    c.rollback();
}

```

