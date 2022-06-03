### AOP 실무 주의사항

### 프록시와 내부 호출 - 문제

스프링은 프록시 방식의 `AOP` 를 사용한다.

따라서 `AOP` 를 적용하려면 항상 **프록시**를 통해서 대상 객체를 호출해야 한다.

이렇게 해야 **프록시**에서 먼저 `Advice` 를 호출하고, 이후에 대상 객체를 호출한다.

만약 **프록시**를 거치지 않고 대상 객체를 직접 호출하게 되면 `AOP` 가 적용되지 않고,  `Advice` 도 호출되지 않는다.

`AOP` 을 적용하면 스프링은 대상 객체 대신에 **프록시**를 스프링 빈으로 등록한다. 따라서 스프링은 의존관계 주입시 항상 **프록시 객체**를 주입한다.

**프록시 객체가 주입되기 때문에 대상 객체를 직접 호출하는 문제는 일반적으로 발생하지 않는다**. 

하지만, 대상 **객체의 내부에서 메서드 호출**이 발생하면 **프록시를 거치지 않고 대상 객체를 직접 호출**하는 문제가 발생한다.

```jsx
public class CallServiceV0 {
 public void external() {
	 log.info("call external");
	// 내부에서 호출하여 실제 객체에서 동작하므로 프록시 X
	 internal(); //내부 메서드 호출(this.internal())
 }

 public void internal() {
	 log.info("call internal");
 }
}
```


`external` 을 호출하면 `Advice` 를 호출하고  실제 객체의 메서드를 호출한다.

그리고 `external` 안에서 실행한 `internal` 은 `Advice` 가 호출되지 않는다.

프록시를 통한 호출이 아닌, 직접 호출이기 때문에 문제가 발생한다.

---

**프록시 방식의 AOP 한계**

스프링은 프록시 방식의 `AOP` 를 사용한다. 프록시 방식의 `AOP` 는 메서드 내부 호출에 프록시를 적용할 수 없다.

**참고**

실제 코드에 `AOP` 를 직접 적용하는 `AspectJ` 를 쓰면 이런 문제는 발생하지 않는다. 직접 로직안에 `AOP` 로직을 작성하기 떄문에 내부 호출을 해도 적용이된다.

하지만, **로드 타임 위빙** 등을 써야하는데, 설정이 복잡하며 `JVM` 옵션도 줘야한다.

### 대안1 - 자기 자신 주입

```jsx
@Slf4j
@Component
public class CallServiceV1 {

	 private CallServiceV1 callServiceV1;

 @Autowired
 public void setCallServiceV1(CallServiceV1 callServiceV1) {
	 this.callServiceV1 = callServiceV1;
 }

 public void external() {
	 log.info("call external");
	 callServiceV1.internal(); //외부 메서드 호출
 }

 public void internal() {
	 log.info("call internal");
 }
}
```

**생성자 주입을 할 경우 순환 사이클로 인해 실패한다.**

`setter` 주입을 할 떄는 스프링 빈 생성 타이밍과 달라서 적용이 가능하다.

`setter` 를 통해 주입받을 때, 주입 대상은 실제 객체가 아닌 스프링 빈으로 등록되어 있는 **프록시 객체**이다.

```jsx
@Import(CallLogAspect.class)
@SpringBootTest
class CallServiceV1Test {

 @Autowired
 CallServiceV1 callServiceV1;

 @Test
 void external() {
 callServiceV1.external();
 }
}
```

실행 결과로 실제 객체를 호출하는 것이 아니라 **프록시 인스턴스**를 통해서 호출한다.

**주의**

스프링 부트 2.6버전 부터 순환참조를 기본적으로 금지하게 되었다.

따라서 오류메세지가 나오면서 실행이 되지 않게 되었다.

```jsx
Error creating bean with name 'callServiceV1': Requested bean is currently increation: Is there an unresolvable circular reference?
```

**이 문제를 해결하려면 `application.properties` 에 다음을 추가해야 한다.
`spring.main.allow-circular-references=true`**

**앞으로 있을 다른 테스트에도 영향을 주기 때문에 스프링 부트 2.6 이상이라면 이 설정을 꼭 추가해야 한다**

---

### 대안2 - 지연 조회

**생성자 주입이 실패하는 이유는 자기 자신을 생성하면서 주입해야 하기 때문이다.** 이 경우 수정자 주입을 사용하거나 **지연 조회**를 사용하면 된다.

스프링 빈을 지연해서 조회하는 경우 `ObjectProvider` , `ApplicationContext` 를 사용하면 된다. 하나의 빈만 가져올 것이기 때문에 `ObjectProvider` 를 쓰는 것이 좀 더 낫다.

```jsx
/**
 * ObjectProvider(Provider), ApplicationContext를 사용해서 지연(LAZY) 조회
 */
@Slf4j
@Component
@RequiredArgsConstructor
public class CallServiceV2 {
// private final ApplicationContext applicationContext;
 private final ObjectProvider<CallServiceV2> callServiceProvider;

 public void external() {
 log.info("call external");
	// CallServiceV2 callServiceV2 = 
	applicationContext.getBean(CallServiceV2.class);
	 CallServiceV2 callServiceV2 = callServiceProvider.getObject();
	 callServiceV2.internal(); //외부 메서드 호출
 }

 public void internal() {
	 log.info("call internal");
 }
}
```

`ObjectProvider` 는 객체를 **스프링 컨테이너**에서 조회하는 것을 **스프링 빈 생성 시점**이 아니라 **실제 객체를 사용하는 시점**으로 **지연**할 수 있다.

`callServiceProvider.getObject()` 를 호출하는 시점에 **스프링 컨테이너**에서 빈을 조회한다. 여기서 자기 자신이 아닌 **프록시 객체**를 주입 받으므로 **순환 사이클**이 발생하지 않는다.

---

### 대안3 - 구조 변경

가장 나은 대안으로 **내부 호출이 발생하지 않도록 구조를 변경하는 것이다.**

실제로 이 방법이 가장 권장된다.

```jsx
public class CallServiceV3 {
 private final InternalService internalService;
 public void external() {
 log.info("call external");
 internalService.internal(); //외부 메서드 호출
 }
}
```

```jsx
public class InternalService {
 public void internal() {
 log.info("call internal");
 }
}
```


내부 호출 자체가 사라지고 `callService` → `internalService` 를 호출하는 구조로 변경하여 `AOP` 가 자연스레 적용된다.

여기서 구조를 변경한다는 것은 이렇게 단순하게 분리하는 것 뿐만 아니라 다양한 방법들이 있을 수 있다.

예를 들어서 다음과 같이 클라이언트에서 둘다 호출하는 것이다.

`클라이언트` → `external()`
`클라이언트` → `internal()`

물론 이 경우 `external()` 에서 `internal()` 을 내부 호출하지 않도록 코드를 변경해야 한다. 

그리고 클라이언트 `external()` , `internal()` 을 모두 호출하도록 구조를 변경하면 된다. (물론 가능한 경우에 한해서)
