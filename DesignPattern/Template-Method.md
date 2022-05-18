### Template Method Pattern


#### 정의

`템플릿 메서드 패턴` 은 이름 그대로 **템플릿** 을 사용하는 방식이다.

**템플릿** 은 기준이 되는 거대한 틀을 의미하는데, 그 틀에 변하지 않는 부분을 몰아두고 일부 변하는 부분을 별도로 호출해서 해결한다.


```java
public abstract class AbstractTemplate {
 
public void execute() {
	 long startTime = System.currentTimeMillis();
	 //비즈니스 로직 실행
	 call(); //상속
	 //비즈니스 로직 종료
	 long endTime = System.currentTimeMillis();
	 long resultTime = endTime - startTime;
	 log.info("resultTime={}", resultTime);
 }

 protected abstract void call();
}
```

`AbstractTemplate` 에 변하지 않는 부분인 **시간측정 로직** 을 만들어두었다.

이것이 하나의 **템플릿** 이 되고, 변하는 부분은 `call()` 메서드를 통해서 호출하여 처리하게 한다.

즉, 부모 클래스에는 변하지 않는 템플릿 코드를 둔다. 

그리고 변하는 부분은 자식 클래스에서 두고 `상속` 과 `오버라이딩`을 사용해서 처리한다.

```java
@Slf4j
public class SubClassLogic1 extends AbstractTemplate {
	 @Override
	 protected void call() {
	 log.info("비즈니스 로직1 실행");
	 }
}

@Slf4j
public class SubClassLogic2 extends AbstractTemplate {
   @Override
   protected void call() {
   log.info("비즈니스 로직2 실행");
 }
}
```

### 장점

1. 공통으로 발생하는 로그로직과 비즈니스 로직을 분리하여 로그 로직을 변경할 경우 단순히 템플릿 코드만을 변경하며

하나의 코드만을 변경함으로써 변경에 쉽게 대처할 수 있는 구조로 만들어진다.

2. `상속` 과 `오버라이딩` 을 통한 `다형성` 으로 문제를 해결하는 것이다.


### 단점

1. `상속` 을 사용한다. 

그렇기 떄문에 `상속` 에서 오는 단점을 그대로 가지고 가는데, 특히 자식 클래스가 부모 클래스와 **컴파일 시점** 에 강하게
결합되는 문제가 있다. 이것은 `의존관계` 의 문제이며, 자식 클래스에서는 부모 클래스의 기능을 전혀 사용하지 않음에도 불구
해당 패턴을 위해 상속을 받아 쓴다.

2. 부모 클래스에 강하게 의존한다.

즉, **자식 클래스의 코드에 부모 클래스의 코드가 명확하게 적혀있다는 뜻이다.**

부모 클래스의 기능을 전혀 사용하지 않는데, 코드를 알아야 한다는 것은 `좋은 설계` 가 아니다.

이런 의존관계에 의해서 부모의 변경이 자식에게 영향을 미친다.


### 개선방법

이 방법과 비슷한 역할을 하며 `상속` 의 단점을 제거하는 **전략 패턴** 이 있다.

**전략 패턴** 은 `상속` 대신 `인터페이스`를 활용함으로써 좀 더 유연한 처리방법을 제공한다.


