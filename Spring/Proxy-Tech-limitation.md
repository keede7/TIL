## 프록시 기술의 한계

#### 타입 캐스팅

`JDK 동적 프록시` 와 `CGLIB` 를 사용해서 `AOP` 프록시를 만드는 방법에는 각각 장단점이 있다. 

`JDK 동적 프록시` 는 인터페이스가 필수이고, 인터페이스를 기반으로 **프록시**를 생성한다. `CGLIB` 는 구체 클래스를 기반으로 **프록시**를 생성한다.

물론 인터페이스가 없고 **구체 클래스**만 있는 경우에는 `CGLIB`를 사용해야 한다. 그런데 인터페이스가 있는경우에는 `JDK 동적 프록시`나 `CGLIB` 둘중에 하나를 선택할 수 있다.

스프링이 **프록시**를 만들때 제공하는 `ProxyFactory` 에 `proxyTargetClass` 옵션에 따라 둘중 하나를 선택해서 **프록시**를 만들 수 있다.

- `proxyTargetClass=false` : `JDK 동적 프록시` 를 사용해서 인터페이스 기반 프록시 생성
- `proxyTargetClass=true` : `CGLIB` 를 사용해서 구체 클래스 기반 **프록시** 생성

참고로 옵션과 무관하게 인터페이스가 없으면 `JDK 동적 프록시` 를 적용할 수 없으므로 `CGLIB` 를 사용한다.

---

**JDK 동적 프록시의 한계**

인터페이스 기반으로 **프록시**를 생성하는 `JDK 동적 프록시` 는 구체 클래스로 타입 캐스팅이 불가능한 한계가 있다.


```java

@Test
 void jdkProxy() {

	 MemberServiceImpl target = new MemberServiceImpl();
	 ProxyFactory proxyFactory = new ProxyFactory(target);
	 proxyFactory.setProxyTargetClass(false);//JDK 동적 프록시
	 //프록시를 인터페이스로 캐스팅 성공
	 MemberService memberServiceProxy = (MemberService)
	proxyFactory.getProxy();
	 log.info("proxy class={}", memberServiceProxy.getClass());
	 //JDK 동적 프록시를 구현 클래스로 캐스팅 시도 실패, ClassCastException 예외 발생
		 assertThrows(ClassCastException.class, () -> {
		 MemberServiceImpl castingMemberService =
		(MemberServiceImpl) memberServiceProxy;
		 }
	 );

```

이유는 간단하다.

**JDK 동적 프록시**는 인터페이스를 기반으로 만들었기 때문에, 같은 인터페이스를 기반으로 만들어진 다른 구현 클래스를

알 수 있는 방법이 없기 떄문에 **캐스팅이 불가능해진다는 단점**이 존재한다.


**CGLIB를 사용할 경우**

```java

@Test
void cglibProxy() {
 MemberServiceImpl target = new MemberServiceImpl();
 ProxyFactory proxyFactory = new ProxyFactory(target);
 proxyFactory.setProxyTargetClass(true);//CGLIB 프록시
 //프록시를 인터페이스로 캐스팅 성공
 MemberService memberServiceProxy = (MemberService) proxyFactory.getProxy();
 log.info("proxy class={}", memberServiceProxy.getClass());
 //CGLIB 프록시를 구현 클래스로 캐스팅 시도 성공
 MemberServiceImpl castingMemberService = (MemberServiceImpl)
memberServiceProxy;
}

```
**CGLIB** 는 인터페이스의 구현체를 기반으로 프록시를 생성하기 때문에, 캐스팅이 가능해진다.

## 프록시 기술과 한계 - 의존관계 주입

`JDK 동적 프록시` 를 사용하게 끔 했을 경우 예외가 발생한다.

`JDK 동적 프록시` 는 인터페이스를 기반으로 프록시가 만들어지기 때문에, 인터페이스를 주입받을 경우에는

문제는 없지만, 인터페이스를 구현한 구현체를 주입받아야 하는 상황이 발생하는 경우에는 해당 타입이 뭔지 모르기때문에,

주입을 할 수 없다.

```java
  @Autowired
  MemberService memberService ( O ) 
  
  @Autowired
  MemberServiceImpl memberServiceImpl ( X )
```

`CGLIB` 를 사용하는 경우에는 구체 클래스를 기반으로 상속을 받아서 **프록시**가 만들어지기 떄문에
 
 의존관계 주입이 이루어질 수 있다.
 
 
## 프록시 기술과 한계 - CGLIB

`CGLIB` 는 구체 클래스를 상속 받기 때문에 다음과 같은 문제가 있다.

- 대상 클래스에 기본 생성자가 필수
- 생성자 2번 호출 문제
- `final` 키워드 클래스, 메서드 문제

---

**대상 클래스에 기본 생성자 필수**

CGLIB는 구체 클래스를 상속 받는다. 

자바 언어에서 상속을 받으면 자식 클래스의 생성자를 호출할 때 자식 클래스의 생성자에서 부모 클래스의 생성자도 호출해야 한다.

이 부분은 자바 문법 규약이다.

**CGLIB**를 사용할 때 **CGLIB**가 만드는 **프록시**의 생성자는 우리가 호출하는 것이 아니다. 

**CGLIB 프록시**는 대상 클래스를 **상속** 받고, 생성자에서 대상 클래스의 기본 생성자를 호출한다. 

따라서 대상 클래스에 기본 생성자를 만들어야 한다.

 ---
 
**생성자 2번 호출 문제**

CGLIB는 구체 클래스를 상속 받는다. 

자바 언어에서 상속을 받으면 자식 클래스의 생성자를 호출할 때 부모 클래스의 생성자도 호출해야 한다. 

1. 실제 **target 객체**를 생성할 때
2. **프록시 객체**를 생헝할 때 부모 클래스의 생성자 호출


**final 키워드 클래스, 메서드 사용 불가**
`final` 키워드가 클래스에 있으면 **상속**이 불가능하고, 메서드에 있으면 **오버라이딩**이 불가능하다. 

CGLIB는 상속을 기반으로 하기 때문에 두 경우 **프록시**가 생성되지 않거나 정상 동작하지 않는다.

프레임워크 같은 개발이 아니라 일반적인 웹 애플리케이션을 개발할 때는 `final` 키워드를 잘 사용하지 않는다. 

따라서 이 부분이 특별히 문제가 되지는 않는다.

**정리**
**JDK 동적 프록시는 대상 클래스 타입으로 주입할 때 문제가 있고,
CGLIB는 대상 클래스에 기본 생성자 필수, 생성자 2번 호출 문제가 있다.**





