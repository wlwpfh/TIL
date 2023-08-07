## Master DB - Slave DB
 
회사의 운영 전용 데이터베이스에서 `WRITE` 전용 DB, `READ` 전용 DB를 따로 두었다.  
왜일까 ..  

### 둘을 따로 두는 이유   
만약 하나의 데이터베이스에서 모든 작업을 하는 경우에 트래픽이 폭발적으로 증가할 수 있다.  
그 경우에 아무리 성능이 뛰어나더라도 모든 트래픽을 견디기 불가능하다.   

1. 트래픽이 많아져 데이터베이스 장개 발생 확률이 높아진다 -> 운영 중인 서비스에 문제가 발생할 수 있다.    
=> **가용성의 문제**     
2. 트래피으로 인해 쿼리 수행 빈도가 높아진다 -> 작업 병목현상이 발생할 수 있다.   
=> **부하의 문제**  

<br/>

이러한 이유로 인해 `Master DB`를 복제한 `Slave DB`를 하나 더 둔 것이다.    
`Master DB`의 경우에는 데이터 동ㅇ시성이 아주 높게 요구되는 트랜잭션을 담당한다.    
`Slave DB`의 경우에는 읽기만 하여 데이터의 동시성이 꼭 보장될 필요가 없는 경우를 담당한다.  

<br/>

### 둘을 따로 두었을 때의 장점 

**1. 가용성이 높아진다.**   
`Master DB`에 장애가 발생할 경우, `Slave DB`를 `Master`로 승격시켜 서비스를 빠르게 복구할 수 있다.  
**2. 부하 분산이 가능하다.**    
`Master DB`에서는 `WRITE`, `Slave DB`에서는 `READ`만 담당하여 부하를 분산시킬 수 있다.

<br/>

### 코드 예제   
```java
    public class DynamicRoutingDataSource extends AbstractRoutingDataSource {
    @Override
    protected Object determineCurrentLookupKey() {
        boolean isReadOnly = TransactionSynchronizationManager.isCurrentTransactionReadOnly();

        if (isReadOnly)
            return DataSourceType.Slave;
        else
            return DataSourceType.Master;
    }

    public enum DataSourceType {
        Master, Slave
    }
}
```

연결할 데이터베이스가 `Master, Slave` 두 개이기 때문에 동적으로 원하는 `DataSource`를 선택할 수 있어야 한다.
`AbstractRoutingDataSource`를 통해 데이터베이스 동적 라우팅을 설정했다.  
그 다음에 현재 사용 중인 `Transaction`이 정말 `readOnly`로 설정되어 있는지 `isReadOnly`값을 통해 확인한다.  
해당 값에 따라 `Master`와 `Slave`로 구분한다.    
가져오는 데이터베이스를 `Slave`, 값의 변화가 발생하는 데이터베이스를 `Master`로 칭한다.  

<br/>

참고: https://cornswrold.tistory.com/561
