## 스프링의 트랜잭션 추상화

스프링은 이런 고민을 다 해두었다. 우리는 스프링이 제공하는 **트랜잭션 추상화 기술**을 사용하면 된다. 

심지어 데이터 접근 기술에 따른 트랜잭션 구현체도 대부분 만들어 두어서 가져다 쓰기만 하면 된다.

스프링 트랜잭션 추상화의 핵심은 `PlatformTransactionManager` 인터페이스이다.

```java
public interface PlatformTransactionManager extends TransactionManager {
  TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;
  
  void commit(TransactionStatus status) throws TransactionException;
  void rollback(TransactionStatus status) throws TransactionException;
}
```

- `getTransaction()` : 트랜잭션을 시작한다.
    - 이름이 `getTransaction()` 인 이유는 기존에 이미 진행중인 트랜잭션이 있는 경우 해당 트랜잭션에 참여할 수 있기 떄문이다.
- `commit()` : 트랜잭션을 `commit`한다.
- `rollback()` : 트랜잭션을 `rollback`한다.

---

기본적으로 트랜잭션을 시작하기 위해서는 `Connection` 이 필요하다.  그래서 스프링에서는 트랜잭션 매니저가

`DataSource` 를 통해 `Connection` 을 만들고 트랜잭션을 시작한다.

내부적으로 트랜잭션 매니저는 트랜잭션 동기화 매니저를 사용하는데 트랜잭션 동기화 매니저는 `ThreadLocal` 을 사용해서

`Connection` 을 보관하고 동기화 할수 있게 해준다. 


**트랜잭션 동기화 매니저**

다음 클래스에서 **쓰레드 로컬**을 사용하는 것을 확인할 수 있다

`org.springframework.transaction.support.TransactionSynchronizationManager`

---

### DataSourceUtils Class

`DataSourceUtils` 클래스에서 트랜잭션 매니저를 통해서 만들어낸 `Connection` 에 대한 여러가지 제어기능을 제공한다.


**DataSourceUtils.getConnection()**

- `getConnection()` 에서 `DataSourceUtils.getConnection()` 를 사용하도록 변경된 부분을 주의 해야 한다.
- `DataSourceUtils.getConnection()` 은 다음과 같이 동작한다.
    - **트랜잭션 동기화 매니저**가 관리하는 커넥션이 있으면 해당 커넥션을 반환한다.
    - **트랜잭션 동기화 매니저**가 관리하는 커넥션이 없으면 새로운 커넥션을 생성해서 반환한다.
    

**DataSourceUtils.releaseConnection()**

- `close()` 에서 `DataSourceUtils.releaseConnection()` 을 사용하도록 변경된 부분을 주의해야 한다. 커넥션을 `con.close()` 를 사용해서 직접 닫으면 커넥션이 유지되지 않는 문제가 발생한다. 이 커넥션은 이후 로직은 물론이고, 트랜잭션을 종료(커밋, 롤백)할 떄 까지 살아 있어야 한다.
- `DataSourceUtils.releaseConnection()` 을 사용하면 커넥션을 바로 닫지 않는다.
    - 트랜잭션을 사용하기 위해 동기화된 커넥션은 닫지 않고 그대로 유지한다.
    - **트랜잭션 동기화 매니저**가 관리하는 커넥션이 없으면 해당 커넥션을 닫는다


**사용 예시**
```java
 public void accountTransfer(String fromId, String toId, int money) throws SQLException {
 //트랜잭션 시작
 TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
	 try {
	 //비즈니스 로직
     bizLogic(fromId, toId, money);
     transactionManager.commit(status); //성공시 커밋
	 } catch (Exception e) {
     transactionManager.rollback(status); //실패시 롤백
     throw new IllegalStateException(e);
	 }
 }
```

### 정리
트랜잭션 관련 처리는 했지만, 결국 비즈니스 로직에 트랜잭션 코드가 들어가 있다.

이러한 점을 또 해결하기 위해서 다른방식을 구현하고 있다.


