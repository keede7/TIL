## Proxy Factory



스프링은 유사한 구체적인 기술들이 있을 떄, 그것들을 통합해서 일관성 있게 접근할 수 있고, 더욱 편리하게 사용할 수 있는 추상화된 기술을 제공한다.

스프링은 **동적 프록시**를 통합해서 편리하게 만들어주는 `프록시 팩토리` 라는 기능을 제공한다.  이전에는 상황에 따라서 **JDK 동적 프록시**를 사용하거나 **CGLIB**를 사용했다면 이제는 이 `프록시 팩토리` 하나로 편리하게 **동적 프록시**를 생성할 수 있다.



스프링은 이 문제를 해결하기 위해서 부가 기능을 적용할 때 `Advice` 라는 새로운 개념을 도입했다. 

개발자는 `InvocationHandler`, `MethodInterceptor` 를 신경쓰지 않고, `Advice` 만 만들면 된다.

결론적으로 `InvocationHandler`, `MethodInterceptor`는 `Advice` 를 호출하게 된다.

프록시 팩토리를 사용하면 `Advice` 를 호출하는 전용  `InvocationHandler`, `MethodInterceptor` 를 내부에서 사용한다.

---

스프링은 `Pointcut` 이라는 개념을 도입해서 이 문제를 일관성 있게 해결한다.


**Advice 만들기**

`Advice` 는 프록시에 적용하는 부가 기능 로직이다. 이것은 **JDK 동적 프록시**가 제공하는 `InvocationHandler` 와  **CGLIB**가 제공하는 `MethodInterceptor`와 개념이 유사하다. 둘을 개념적으로 추상화한 것이다.

프록시 팩토리를 사용하면 둘 대신 `Advice`를 사용하면 된다.

```jsx
public interface MethodInterceptor extends Interceptor {
 Object invoke(MethodInvocation invocation) throws Throwable;
}
```

- `MethodInvocation`

내부에는 **다음 메서드를 호출하는 방법, 현재 프록시 객체 인스턴스, 메서드정보** 등이 포함되어 있다.

기존에 파라미터로 제공하던 부분이 이 안에 모두 들어있다.

```jsx
@Slf4j
public class TimeAdvice implements MethodInterceptor {

 @Override
 public Object invoke(MethodInvocation invocation) throws Throwable {
	 log.info("TimeProxy 실행");
	 long startTime = System.currentTimeMillis();

	 Object result = invocation.proceed();

	 long endTime = System.currentTimeMillis();
	 long resultTime = endTime - startTime;
	 log.info("TimeProxy 종료 resultTime={}ms", resultTime);
	 return result;
 }
}
```

- `Object result = invocation.proceed()`

`invocation.proceed()` 를 호출하면 `targer` 클래스를 호출하고  그 결과를 받는다. 하지만 기존 코드와 다르게 `target` 이 보이지 않는데, `target` 클래스의 정보는 모두 `invocation` 안에 포함되어 있는 것이다.

그 이유는 **프록시 팩토리**로 프록시를 생성하는 단계에서 이미 `target` 정보를 파라미터로 받기 떄문이다.

```jsx
@Slf4j
public class ProxyFactoryTest {

 @Test
 @DisplayName("인터페이스가 있으면 JDK 동적 프록시 사용")
 void interfaceProxy() {
	
	 ServiceInterface target = new ServiceImpl();
	 ProxyFactory proxyFactory = new ProxyFactory(target);
	 proxyFactory.addAdvice(new TimeAdvice());
	
	 ServiceInterface proxy = (ServiceInterface) proxyFactory.getProxy();
	 log.info("targetClass={}", target.getClass());
	 log.info("proxyClass={}", proxy.getClass());
	
	 proxy.save();
	
	 assertThat(AopUtils.isAopProxy(proxy)).isTrue();
	 assertThat(AopUtils.isJdkDynamicProxy(proxy)).isTrue();
	 assertThat(AopUtils.isCglibProxy(proxy)).isFalse();
 }
}
```

- `new ProxyFactory(target)` : **프록시 팩토리**를 생성할 떄, 생성자에 프록시의 호출대상을 함께 넘겨주고 그 인스턴스정보를 기반으로 프록시를 생성한다. 그 인스턴스에 **인터페이스**가 있으면 **JDK 동적 프록시**를, 구체 클래스만 있다면 **CGLIB를 통해 동적 프록시**를 생성한다.
- `proxyFactory.addAdvice(new TimeAdvice())` : 프록시 팩토리를 통해서 만든 프록시가 사용할 부가 기능 로직을 설정한다. `JDK 동적 프록시` 가 제공하는 `InvocationHandler` 와 `CGLIB` 가 제공하는 `MethodInterceptor` 개념과 유사하다. 이렇게 프록시가 제공하는 부가 기능 로직을 `Advice` 라고 한다.
- `proxyFactory.getProxy()` : 프록시 객체를 생성하고, 그 결과를 받는다.

### 예제 코드 2

```jsx
@Test
    @DisplayName("구체 클래스만 있으면 CGLIB 사용")
    void concreteProxy() {
        ConcreteService target = new ConcreteService();
        ProxyFactory proxyFactory = new ProxyFactory(target);
        proxyFactory.addAdvice(new TimeAdvice());
        ConcreteService proxy = (ConcreteService) proxyFactory.getProxy();

        log.info("targetClass={}", target.getClass());
        log.info("proxyClass={}", proxy.getClass());

        proxy.call();

        assertThat(AopUtils.isAopProxy(proxy)).isTrue();
        assertThat(AopUtils.isJdkDynamicProxy(proxy)).isFalse();
        assertThat(AopUtils.isCglibProxy(proxy)).isTrue();
    }
```

`프록시 팩토리` 는 인터페이스 없이 구체 클래스만 있으면 `CGLIB` 를 사용해서 **프록시**를 적용한다.

```jsx
@Test
    @DisplayName("ProxyTargetClass 옵션을 사용하면 인터페이스가 있어도 CGLIB를 사용하고, 클래스기반 프록시 사용")
    void proxyTargetClass() {
        ServiceInterface target = new ServiceImpl();
        ProxyFactory proxyFactory = new ProxyFactory(target);
        proxyFactory.setProxyTargetClass(true); //중요
        proxyFactory.addAdvice(new TimeAdvice());
        ServiceInterface proxy = (ServiceInterface) proxyFactory.getProxy();

        log.info("targetClass={}", target.getClass());
        log.info("proxyClass={}", proxy.getClass());

        proxy.save();

        assertThat(AopUtils.isAopProxy(proxy)).isTrue();
        assertThat(AopUtils.isJdkDynamicProxy(proxy)).isFalse();
        assertThat(AopUtils.isCglibProxy(proxy)).isTrue();
    }
```

인터페이스가 있지만, `CGLIB` 를 사용해서 인터페이스가 아닌 클래스 기반으로 **동적 프록시**를 만드는 방법이다.

`프록시 팩토리` 는 `proxyTargetClass` 라는 옵션을 제공하는데, 이 옵션에 `true` 값을 넣으면 **인터페이스**가 있어도 강제로 `CGLIB`를 사용한다.

그리고 인터페이스가 아닌 클래스 기반의 **프록시**를 만들어 준다.

---

**프록시 팩토리의 기술 선택 방법**

- 대상에 `인터페이스` 가 있으면: **JDK 동적 프록시, 인터페이스 기반 프록시**
- 대상에 `인터페이스` 가 없으면 : **CGLIB, 구체 클래스 기반 프록시**
- `proxyTargetClass = true` : **CGLIB, 구체 클래스 기반 프록시, 인터페이스 여부와 상관 없다**.

### 정리

- `프록시 팩토리` 의 서비스 추상화 덕분에 구체적인 **CGLIB, JDK 동적 프록시** 기술에 의존하지 않고, 매우 편리하게 동적 프록시를 생성 할 수 있다.
- **프록시**의 부가 기능 로직도 특정 기술에 종속적이지 않게 `Advice` 하나로 편리하게 사용할 수 있었다. 이것은 **프록시 팩토리**가 내부에서 **JDK 동적 프록시**인 경우 `InvocationHandler` 가 `Advice` 를 호출하도록 개발해두고, **CGLIB** 인 경우 `MethodInterceptor`  가 `Advice` 를 호출하도록 기능을 개발해두었기 떄문이다.

