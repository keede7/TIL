## BeanPostProcessor ( 빈 후처리기 )

`Spring` 이 `Bean` 저장소에 등록할 목적으로 생성한 객체를 `Bean` 저장소에 등록하기 전에 조작하고 싶다면

**빈 후처리기** 를 사용하면 된다.

이름 그대로 `Bean` 을 생성한 후에 무언가를 처리하는 용도로 쓰인다.

이떄, **객체를 조작**할 수 도 있고, 아예 **다른 객체로 바꿔치기** 할 수 도 있다.


### 빈 등록과정 및 빈 후처리기

1. **생성** : `Spring Bean` 대상이 되는 객체를 생성한다.

2. **전달** : 생성된 객체를 `Bean` 저장소에 등록하기 전에 **빈 후처리기**에 전달한다.

3. **후 처리 작업** : **빈 후처리기**는 전달된 스프링 빈 객체를 조작하거나 다른 객체로 바꿔치기 할 수 있다.
 
4. **등록** : **빈 후처리기**는 빈을 반환한다. 전달된 빈을 그대로 반환하면 해당 빈이 등록되고 바꿔치기 하면 다른 객체가 빈 저장소에 등록된다.


### BeanPostProcessor Interface
```java
public interface BeanPostProcessor {
 Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException
 Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException
}

```

빈 후처리기를 사용하려면 `BeanPostProcessor` 인터페이스를 구현하고, `Spring Bean` 으로 등록하면 된다.

- `postProcessBeforeInitializtion` : 객체 생성 이후 `@PostConstruct` 같은 초기화가 발생하기 전에 호출되는 포스트 프로세서이다.

- `postProcessAfterInitialization` : 객체 생성 이후 `@PostConstruct` 같은 초기화가 발생한 다음에 호출되는 포스트 프로세서이다.


#### 정리

**빈 후처리기**는 `Bean` 을 조작하고 변경할 수 있는 **후킹 포인트**이다.

빈 객체를 조작하거나 심지어 다른 객체로 바꿔버릴 수 도 있다.

여기서 **조작**은 **해당 객체의 특정 메서드를 호출하는 것**을 뜻한다.

특히, **컴포넌트 스캔**의 대상이 되는 빈들은 중간에 조작할 방법이 없는데,

**빈 후처리기**를 사용하면 개발자가 등록하는 모든 빈을 중간에 조작할 수 있다.

**이 말은, 빈 객체를 프록시 객체로 교체하는 것도 가능하다는 뜻이다.**


그 예시로, `@PostConstruct` 가 있는데, 이것은 해당 어노테이션이 붙은 초기화 메서드를 한 번 실행하는 것이다.

쉽게 말해 생성된 빈을 한번 조작하는 것이다.
