### 준영속 상태와 지연로딩

`트랜잭션`은 보통 서비스 계층에서 시작하므로 서비스 계층이 끝나는 시점에 

`트랜잭션`이 종료되면서 `영속성 컨텍스트`도 함께 종료된다. 

따라서 `Service` 와 `Repository` 계층에서는 영속성 컨텍스트가 관리되어 영속상태를 유지하지만, 

`Controller` , `View` 같은  `Presentation` 계층에서는 준영속상태가 된다.

```java
@Entity
public class Order {

	@Id @GeneratedValue
	private Long id;

	@ManyToOne(fetch = FetchType.LAZY)
	private Member member;
}
```

컨테이너 환경의 기본전략으로 트랜잭션이 없는 `Presentation` 계층에서 엔티티는 준영속 상태가 된다.

따라서 `변경감지` , `지연로딩` 이 발생하지 않고 컨트롤러 로직상에서 지연로딩을 할 경우 예외가 발생한다.

```java
class OrderController{
	
	public String view(Long orderId) {

		Order order = orderService.findOne(orderId);
		Member member = order.getMember();
		member.getName(); // 지연 로딩 시 예외 발생
	}
}
```

- 준영속 상태와 변경 감지

변경 감지 기능은 영속성 컨텍스트가 살아있는 `Serivce` 계층 까지 동작하고 영속성 컨텍스트가 종료된 `Presentation` 계층에서는 동작하지 않는다. 

보통 변경 감지 기능은 `Service` 계층에 서 비즈니스 로직을 수행하면서 발생한다.

단순히 데이터를 보여주기만 하는 `Presentation` 계층에서 데이터를 수정할 일은 거의 없다. 

오히려 `변경 감지` 기능이 `Presentation` 계층에서도 동작하면 애플리케이션 계층이 가지는 책임이 모호해 진다. 

무엇보다 어떻게 변경했는지 `Presentation` 계층을 전부 찾아야 하므로 유지보수도 어렵다.

웬만하면 비즈니스 로직은 `Service` 계층에서 끝내고 `Presentation` 계층은 데이터를 보여주는 데 집중 해야한다. 

그래서 `변경 감지` 기능이 `Presentation` 계층에서 동작하지 않는 것은 별로 문제가 되지 않는다.

---

준영속 상태의 지연로딩 문제에 대한 해결방법은 2가지가 있다.

- 뷰가 필요한 엔티티를 미리 로딩하는 법.
- `OSIV` 를 이용해서 엔티티를 항상 영속상태로 유지하는 법.

1) 뷰가 필요한 엔티티를 미리 로딩하는 방법은 어디서 미리 로딩하냐에 따라 3가지 방법이 있다.

- 글로벌 페치 전략 수정
- JPQL 페치 조인
- 강제로 초기화

### 글로벌 페치 전략 수정

가장 간단한 방법은 연관관계의 페치 전략을 즉시로딩으로 변경하는 것이다.

```java
@ManyToOne(fetch = FetchType.EAGER)
```

애플리케이션에서 항상 이 엔티티를 로딩할 때마다 해당 전략을 사용하므로 `글로벌 페치 전략` 이라고 한다.

A 엔티티를 조회하면 연관된 B 엔티티를 함께 항상 로딩한다.

하지만 2가지 단점이 존재한다.

1) 사용하지 않는 엔티티를 조회한다.

2) `N+1` 문제가 발생한다.

사용하지 않는 엔티티를 조회하는 것의 예로 A라는 엔티티를 조회했을 때 B라는 엔티티는 즉시로딩전략에 의해서 B 엔티티 정보를 사용하지 않는 데도 함께 조회한다.

그리고 `N+1` 문제는 `JPA` 에서 항상 조심해야 할 문제인데.

엔티티를 조회할 때 즉시 로딩전략일 경우, DB에 JOIN 쿼리를 사용해서 한번에 연관된 데이터까지 조회 한다.

`JPQL` 로 조회를 할 때는,  글로벌 페치 전략을 참고하지 않고 SQL을 만들어 내는데,

연관된 엔티티 또한 로딩을 하게 되므로 연관된 엔티티를 조회하는 쿼리를 추가적으로 발생시킨다.

그래서 조회성능에 문제가 생긴다. 이러한 `N+1` 문제는 `JPQL` 페치조인 으로 해결할 수 있다.

### JPQL 페치 조인

```java
// JPQL
select o from Order o join fetch o.member

// SQL
select o.* , m.* from Order o join Member m 
on o.MEMBER_ID = m.MEMBER_ID
```

페치조인은 JOIN 명령어 마지막에 `fetch` 를넣어주면 된다.

이렇게 하면 SQL은 페치 조인 대상까지 함께 조회를 하기 때문에 `N+1` 이 발생하지 않는다.

### JPQL 페치 조인 단점

페치 조인이 현실적인 대안이긴 하지만 무분별하게 사용할 경우 레포지토리 메서드가 증가할 수 있다.

알게모르게 `Presentation` 계층이 `DAO` 계층에 침범하는 것.

만약 A 화면에는 A엔티티만 필요하고 B화면에는 A, B 엔티티 둘다 필요할 경우 최적화를 위해 2개의 메서드를 만들어야 한다.

이렇게 최적화는 가능하지만 `View` 와 `Repository` 의 논리적  의존관계가 발생한다.

다른 대안으로는 2개의 엔티티를 조회하는 메서드를 그냥 만들고  A, B 화면에서 모두 해당 메서드를 사용하게 하는 것이다.

A화면에서는 필요하지 않는 데이터를 로딩하기 떄문에 조금 느려질 수는 있으나 1번의 쿼리로 여러 데이터를 한번에 가져오기 떄문에 성능에 미치는 영향이 적을 것이다.



**적절한 타협점을 찾아서 사용하는 것이 합리적일 것이다.**



### 강제로 초기화

영속성 컨텍스트가 살아 있을 때 `Presentation` 계층이 필요한 엔티티를 강제로 초기화 해서 반환하는 방법.

```java
@Transactional
public Order findOrder(id) {
	Order order = orderRepository.findOrder(id);
	order.getMember().getName();
	return Oorder;}}
```

지연 로딩 전략이라고 가정 했을 떄 연관된 엔티티는 `프록시` 객체이기 때문에 `getName()` 을 호출하여 실제 값을 사용하도록 초기화를 한다.

이렇게 이미 초기화를 해두면 `Presentation` 계층에서 준영속 상태라도 해당 값을 사용할 수 있게 된다.

하지만, `Service` 계층을 `Presentation` 계층이 침범하는 것은 별로 좋지않다.

`Service`는 비즈니스 로직을 담당해야 하는데 이처럼 `Presentation` 계층을 위한 일까지는 좋지 않다.
