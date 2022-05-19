### Strategy Pattern

`전략 패턴` 은 변하지 않는 부분을 `Context` 라는 곳에 두고, 변하는 부분을 `Strategy` 라는 인터페이스를 만들어서

해당 인터페이스를 구현하도록 해서 문제를 해결한다. 

`상속` 이 아닌 `위임` 으로 문제를 해결하는 것이며, `상속`의 단점을 안고 가는 `템플릿 메서드 패턴` 과 다르게

그 단점을 해결하는 **인터페이스** 를 사용함으로써 해결한다.

여기서 `Context` 를 변하지 않는 템플릿 역할을 하며, `Strategy` 는 변하는 알고리즘 역할을 한다.

```java
public interface Strategy {
 void call();
}

@Slf4j
public class StrategyLogic1 implements Strategy {
	 @Override
	 public void call() {
		 log.info("비즈니스 로직1 실행");
	 }
}

@Slf4j
public class StrategyLogic2 implements Strategy {
	 @Override
	 public void call() {
		 log.info("비즈니스 로직2 실행");
	 }
}
```

```java
@Slf4j
public class ContextV1 {

 private Strategy strategy;

 public ContextV1(Strategy strategy) {
	 this.strategy = strategy;
 }

 public void execute() {
	 long startTime = System.currentTimeMillis();
	 //비즈니스 로직 실행
	 strategy.call(); //위임
	 //비즈니스 로직 종료
	 long endTime = System.currentTimeMillis();
	 long resultTime = endTime - startTime;
	 log.info("resultTime={}", resultTime);
 }

}
```


`전략 패턴` 의 핵심은 `Context` 가 `Strategy` 라는 인터페이스에만 의존한다는 점으로,

덕분에 `Strategy` 의 구현체를 변경하거나 새로 만들어도 `Context` 코드에는 영향을 주지 않는다.

**이것이 스프링에서 의존관계 주입에 사용하는 방식과 같은 전략이다.**


### 또 다른 방식 - 파라미터 전달

이번에는 다르게 `Context` 필드에 `Strategy` 를 주입해서 사용하는 게 아니라

전략을 실행할 때 직접 파라미터로 전달해서 사용해본다.

```java
public class ContextV2 {
 public void execute(Strategy strategy) {
	 long startTime = System.currentTimeMillis();
	 //비즈니스 로직 실행
	 strategy.call(); //위임
	 //비즈니스 로직 종료
	 long endTime = System.currentTimeMillis();
	 long resultTime = endTime - startTime;
	 log.info("resultTime={}", resultTime);
 }
}
```

`ContextV2` 는 전략을 필드로 가지지 않으나, 대신 전략을 `execute(..)` 가 호출될 때마다 항상 파라미터로 전달 받는다.

```java
@Test
 void strategyV1() {
	 ContextV2 context = new ContextV2();
	 context.execute(new StrategyLogic1());
	 context.execute(new StrategyLogic2());
 }
```

**선 조립, 후 실행** 하는 방식이 아니라 `Context` 를 실행할 때 마다 전략을 인수로 전달한다. 클라이언트는 `Context`를 실행하는 시점에 원하는 `Strategy` 를 전달 할 수 있다.

따라서 이전 방식과 비교해서 원하는 전략을 더욱 유연하게 변경 할 수 있다.



