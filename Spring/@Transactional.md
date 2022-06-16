# 스프링 트랜잭션 이해

### 소개

**스프링 트랜잭션 추상화**

각 데이터 접근 기술들은 `트랜잭션`을 처리하는 방식에 차이가 있다.

따라서 그런 문제를 해결하기 위해서 스프링은 **트랜잭션 추상화**를 제공한다.

`트랜잭션` 을 사용하는 입장에서는 스프링 **트랜잭션 추상화**를 통해 동일한 방식으로 사용할 수 있게 된다.

스프링은 `PlatormTransactionManager` 라는 인터페이스를 통해 `트랜잭션` 을 추상화한다.


- 스프링은 트랜잭션을 추상화해서 제공할 뿐만 아니라, 실무에서 사용하는 접근기술에 대한 트랜잭션 매니저의 구현체도 제공한다. 우리는 필요한 구현체를 스프링 빈으로 등록하고 주입받아서 사용하기만 하면 된다.

---

### 스프링 트랜잭션 사용 방식

**선언적 트랜잭션 관리 vs 프로그래밍 방식 트랜잭션 관리**

- **선언적 트랜잭션 관리**
    - `@Transactional` 애노테이션 하나만 선언하면 매우 편리하게 `트랜잭션`을 적용하는 것을 **선언적 트랜잭션 관리**라고 한다.
- **프로그래밍 방식 트랜잭션 관리**
    - **트랜잭션 매니저** 또는 **트랜잭션 템플릿**을 사용해서 트랜잭션 관련 코드를 직접 작성하는 것을 말한다.

프로그래밍 방식을 사용하면 애플리케이션 코드가 `트랜잭션` 기술코드와 결합된다. 

선언적 방식을 사용하는 것이 훨씬 간편하고 실용적이기 떄문에 대부분 **선언적 트랜잭션 관리**를 사용한다.


- `트랜잭션`은 커넥션에 `con.setAutocomiit(false)` 를 지정하면서 시작한다.
- 같은 `트랜잭션` 을 유지하려면 같은 DB커넥션을 써야한다.
- 이것을 위해 스프링 내부에서 동기화 매니저를 사용한다.

---

### 트랜잭션 적용 확인

`@Transactional` 을 통해 **선언적 트랜잭션 방식**을 사용하면 단순히 애노테이션 하나로 `트랜잭션`을 적용할 수 있다.

이 기능은 `AOP` 를 기반으로 동작하기 때문에, 실제로 `트랜잭션` 이 적용되고 있는지 아닌지를 확인하기 어렵다.

**TransactionSynchronizationManager.isActualTransactionActive()**

- 현재 쓰레드에 트랜잭션이 적용되어 있는지 확인할 수 있는 기능이다. 결과가 `ture` 이면 `트랜잭션`이 적용되어 있는 것이다.


### 트랜잭션 적용 위치

스프링은 우선순위는 항상 더 구체적이고 자세한 것이 더 높은 우선순위를 가진다. 이것만 기억하면 스프링에서 발생하는 대부분의 우선순위를 쉽게 기억할 수 있다.

인터페이스와 해당 인터페이스를 구현한 클래스에 애노테이션을 붙일 수 있다면 더 구체적인 클래스가 더 높은 우선순위를 가진다.

```java
@SpringBootTest
public class TxLevelTest {
    @Autowired
    LevelService service;

    @Test
    void orderTest() {
        service.write();
        service.read();
    }

    @TestConfiguration
    static class TxApplyLevelConfig {
        @Bean
        LevelService levelService() {
            return new LevelService();
        }
    }

    @Slf4j
    @Transactional(readOnly = true) // 미적용된 메서드에 적용된다.
    static class LevelService {
        @Transactional(readOnly = false) //  적용된다.
        public void write() {
            log.info("call write");
            printTxInfo();
        }

        public void read() {
            log.info("call read");
            printTxInfo();
        }

        private void printTxInfo() {
            boolean txActive =
                    TransactionSynchronizationManager.isActualTransactionActive();
            log.info("tx active={}", txActive);
            boolean readOnly =
                    TransactionSynchronizationManager.isCurrentTransactionReadOnly();
            log.info("tx readOnly={}", readOnly);
        }
    }
}
```

스프링의 `@Transactional` 에는 2가지 규칙이 있다.

1. 우선순위 규칙
2. 클래스에 적용하면 메서드는 자동적용된다.

**우선순위** 

트랜잭션을 사용할 때 여러가지 옵션을 쓸 수 있다. 

- `LevelService`의 타입에 `readOnly=true` 가 있다.
- `write()` 에는 `readOnly=false` 가 붙어있다.
    - 이렇게 되면 타입에 있는 `readOnly=true`와 해당메서드에 있는 `false` 중 하나를 적용해야 한다.
    - 이때는 클래스보다 메서드가 더 구체적이므로 `false` 옵션이 적용된다.
    

**클래스에 적용하면 메서드는 자동 적용**

- `read()` 는 해당 메서드에 `트랜잭션` 옵션이 없다. 따라서 상위 클래스를 확인해야 하는데 클래스에 `트랜잭션`이 적용되어 있으므로 `readOnly=true` 옵션이 적용된다.

**TransactionSynchronizationManager.isCurrentTransactionReadOnly**

현재 `트랜잭션` 에 적용된 `readOnly` 의 값을 반환한다.

**인터페이스 `@Transacional` 적용**

인터페이스에도 `@Transactional` 을 적용 할 수 있다. 이 경우 다음 순서로 적용된다.

1. 클래스의 메서드 (우선순위가 가장 높다)
2. 클래스의 타입
3. 인터페이스의 메서드
4. 인터페이스의 타입

클래스의 메서드를 찾고, 만약 없으면 클래스의 타입을 찾고 이런식으로 상위 레벨로 탐색을해서 적용을 시킨다.

**인터페이스에 `@Transacional` 을 적용하는 것은 스프링 공식 메뉴얼에서 권장하지 않는 방법이다. `AOP` 를 적용하는 방식에 따라서 인터페이스에 적용하면 `AOP` 가 적용되지 않는 경우도 있기 떄문이다.**


---


### 주의사항 - 프록시 내부 호출1

`@Transactional` 을 사용하면 스프링의 트랜잭션 AOP가 적용된다.

트랜잭션 AOP는 기본적으로 프록시 방식의 AOP를 사용한다.

`@Transactional` 을 적용하면 프록시 객체가 요청을 받아서 트랜잭션을 처리하고 실제 객체를 호출해준다.

따라서 `트랜잭션`을 적용하려면 항상 프록시를 통해서 대상 객체를 호출해야 한다. 이렇게 해서 **프록시**에서 먼저 `트랜잭션`을 적용하고, 이후에 대상 객체를 호출하게 된다.


`AOP` 를 적용하면 스프링은 대상 객체 대신 프록시를 스프링 빈으로 등록한다. 따라서 스프링 의존관계 주입시 항상 프록시 객체가 주입된다.

프록시 객체가 주입되기 떄문에 대상 객체를 직접 호출하는 문제는 일반적으로 발생하지 않지만 대상 객체의 내부에서 메서드 호출이 발생하면 프록시를 거치지않고 대상 객체를 직접 호출하는 문제가 발생한다.

---


### public 메서드만 트랜잭션 적용

스프링의 트랜잭션 AOP 기능은 `public` 메서드에만 `트랜잭션` 을 적용하도록 기본 설정이 되어있따. 그래서 다른 접근제어자에는 적용되지 않는다.

`private` 을 제외한 다른 접근제어자 메서드는 외부에서 호출할 수 있지만, 스프링에서 그냥 막아두었다.

`public` 을 제외한 다른 메서드를 호출할 경우 예외를 발생하진 않지만 `트랜잭션`이 적용되지는 않게된다.

### 주의사항 - 초기화 시점

스프링 초기화 시점에는 트랜잭션 AOP가 적용되지 않을 수 있다.

```java
@SpringBootTest
public class InitTxTest {

    @Autowired
    private Hello hello;

    @Test
    public void go() {

    }

    @TestConfiguration
    static class ConfigV1 {
        @Bean
        public Hello hello() {
            return new Hello();
        }
    }

    @Slf4j
    static class Hello {

        /*
            PostConstruct는 초기화 시점에 트랜잭션 AOP가 적용되기 이전에 동작하기 떄문에
            적용되지 않는다.
         */
        @PostConstruct
        @Transactional
        public void initV1() {
            log.info("initV1...");
            boolean actualTransactionActive = TransactionSynchronizationManager.isActualTransactionActive();
            log.info("active ?? {} ", actualTransactionActive);
        }

        @EventListener(ApplicationReadyEvent.class)
        @Transactional
        public void initV2() {
            boolean actualTransactionActive = TransactionSynchronizationManager.isActualTransactionActive();
            log.info("active ?? {} ", actualTransactionActive);
        }

    }
}
```

초기화 코드 인 `@PostConstruct` 를 쓰면 트랜잭션이 적용되지 않는다.

```java
@PostConstruct
@Transactional
public void initV1() {
 log.info("Hello init @PostConstruct");
}
```

초기화 코드가 먼저 호출되고, 그 다음에 트랜잭션 AOP가 적용되기 떄문이다.

따라서 초기화 시점에는 해당 메서드에서 `트랜잭션` 을 획득할 수 없다.

가장 확실한 방법은 `ApplicationReadyEvent` 를 쓰는 것이다.

```java
@EventListener(value = ApplicationReadyEvent.class)
@Transactional
public void init2() {
 log.info("Hello init ApplicationReadyEvent");
}
```

이 이벤트는 트랜잭션 AOP를 포함한 스프링이 컨테이너가 완전히 생성되고 난 다음에 이벤트가 붙은 메서드를 호출해준다.

### 트랜잭션 옵션

`@Transactional` 

```java
public @interface Transactional {
	String value() default "";
	String transactionManager() default "";
	Class<? extends Throwable>[] rollbackFor() default {};
	Class<? extends Throwable>[] noRollbackFor() default {};
	Propagation propagation() default Propagation.REQUIRED;
	Isolation isolation() default Isolation.DEFAULT;
	int timeout() default TransactionDefinition.TIMEOUT_DEFAULT;
	boolean readOnly() default false;
	 String[] label() default {};
}
```

**value, transactionManager**

`트랜잭션` 을 사용하려면 먼저 스프링 빈으로 등록된 어떤 트랜잭션 매니저를 사용할지 알아야 한다. `@Transactional` 에서도 트랜잭션 프록시가 사용할 트랜잭션 매니저를 지정해주어야 한다.

사용할 트랜잭션 매니저를 지정할 떄는 `value`, `transactionManaer` 둘중 하나에 트랜잭션 매니저의 스프링 빈이름을 적어주면 된다.

만약 사용하는 트랜잭션 매니저가 2개 이상이면 적어서 구분해야한다.

```java
public class TxService {
 @Transactional("memberTxManager")
 public void member() {...}
 @Transactional("orderTxManager")
 public void order() {...}
}
```

**rollbackFor**

예외 발생시 스프링 트랜잭션의 기본 정책은 다음과 같다.

- 언체크 예외인 `RuntimeException`, `Error` 와 그 하위 예외가 발생하면 롤백한다.
- 체크 예외인 `Exception` 과 그 하위 예외들은 커밋한다.

이 옵션을 사용하면 기본 정책에 추가로 어떤 예외가 발생했을 때 롤백을 할지 지정해줄 수 있다.

```java
@Transactional(rollbackFor = Exception.class)
```

이렇게 하면 `Exception` 이 발생해도 롤백을 하게 된다.

`rollbackForClassName` 도 있는데 여기에는 예외 이름을 문자로 넣으면 된다

**noRollbackFor**

`rollbackFor`와 반대되는 개념이다. 기본 정책에 추가로 어떤 예외가 발생했을 때 롤백하면 안되는지 지정할 수 있다. 예외 이름을 문자로 넣을 수 있는 `noRollbackForClassName`도 있다. 

**propagation**

트랜잭션 전파와 관련된 옵션이다. 뒤에서 설명한다.

**isolation**

트랜잭션 격리 수준을 지정할 수 있다. 기본 값은 DB에 설정한 트랜잭션 격리 수준을 사용하는 `Default` 이다. 대부분 DB에서 설정한 기준을 따른다.

**timeout**

트랜잭션 수행시간에 대한 타임아웃을 초 단위로 지정한다. 기본 값은 트랜잭션 시스템의 타임아웃을 사용한다. 운영환경에 따라서 동작하는 경우도 있고, 그렇지 않은 경우도 있다.

**readOnly**

기본적으로 트랜잭션은 읽기, 쓰기 모두 가능한 트랜잭션이 생성된다.

`readOnly=true` 옵션을 쓰면 읽기전용 트랜잭션을 생성한다.

이때는 오로지 읽기 기능만 동작을 하게된다.

`readOnly` 는 크게 3곳에서 사용된다.

- **프레임워크**
    - `JdbcTemplate` 은 변경기능을 실행하면 예외를 던짐
    - `JPA` 는 커밋 시점에 플러시를 호출하지 않는다. 게다가 변경이 필요 없으니 최초 `Entity`와 비교하기 위해서 스냅샷 또한 생성하지 않는다.
- **JDBC 드라이버**
    - 읽기 전용 트랜잭션에서 변경 쿼리가 발생하면 예외를 던진다.
    - 읽기, 쓰기 DB를 구분해서 요청한다. 읽기 DB의 커넥션을 가져와서 사용하게된다.
- **DB**
    - DB에 따라 읽기 전용 트랜잭션의 경우 읽기만 하면 되므로, 내부에서 **성능 최적화**가 발생한다.

### 예외 발생시 트랜잭션

예외 발생시 스프링 트랜잭션 AOP는 예외의 종류에 따라 트랜잭션을 **커밋**하거나 **롤백**한다.

- 언체크 예외인 `RuntimeException`,  `Error` 와 그 하위 예외가 발생하면 트랜잭션을 롤백한다.
- 체크 예외인 `Exception` 과 그 하위 예외가 발생하면 트랜잭션을 커밋한다
- 물론 정상 응답하면 `트랜잭션`을 커밋한다.

```java
@SpringBootTest
public class RollbackTest {
    @Autowired
    RollbackService service;

    @Test
    void runtimeException() {
        assertThatThrownBy(() -> service.runtimeException())
                .isInstanceOf(RuntimeException.class);
    }

    @Test
    void checkedException() {
        assertThatThrownBy(() -> service.checkedException())
                .isInstanceOf(MyException.class);
    }

    @Test
    void rollbackFor() {
        assertThatThrownBy(() -> service.rollbackFor())
                .isInstanceOf(MyException.class);
    }

    @TestConfiguration
    static class RollbackTestConfig {
        @Bean
        RollbackService rollbackService() {
            return new RollbackService();
        }
    }

    @Slf4j
    static class RollbackService {
        //런타임 예외 발생: 롤백
        @Transactional
        public void runtimeException() {
            log.info("call runtimeException");
            throw new RuntimeException();
        }

        //체크 예외 발생: 커밋
        @Transactional
        public void checkedException() throws MyException {
            log.info("call checkedException");
            throw new MyException();
        }

        //체크 예외 rollbackFor 지정: 롤백
        @Transactional(rollbackFor = MyException.class)
        public void rollbackFor() throws MyException {
            log.info("call rollbackFor");
            throw new MyException();
        }
    }

    static class MyException extends Exception {
    }
}
```

스프링은 기본적으로 체크 예외는 비즈니스 의미가 있을 때 사용하고, 런타임예외는 복구 불가능한 예외로 가정한다.

- 체크 예외 : 비즈니스 의미가 있을 때 사용
- 언체크 예외 : 복구 불가능한 예외

무조건 이 정책을 따를 필요는 없다. 앞서 배운 `rollbackFor` 라는 옵션을 사용해서 체크 예외도 롤백하면 된다.
