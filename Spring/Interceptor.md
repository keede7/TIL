### Spring Interceptor


서블릿 필터와 같이 웹과 관련된 공통 관심사항을 효과적으로 해결하는 기술.

+ `서블릿 필터` : 서블릿이 제공하는 기술

+ `스프링 인터셉터` : 스프링 MVC가 제공하는 기술.


둘 다 웹과 관련된 공통 사항을 처리하지만, 적용되는 순서와 범위 및 사용방법이 다르다.

**스프링에서 서블릿은 DispatchServlet 으로 보면 된다**

---

### 인터셉터의 흐름
```
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터 -> 컨트롤러
```


+ `스프링 인터셉터` 는 `DispatchServlet` 과 `Controller` 호출 직전에 호출된다.

+ 스프링 MVC가 제공하는 기능이므로 결국 `DispatchServlet` 이후 등장한다.

+ 스프링 인터셉터도 URL을 적용할 수 있는데 서블릿 URL 패턴과 다르고 매우 정밀하게 설정가능하다.


---

### Interface

스프링 인터셉터를 사용하려면 `HandlerInterceptor` 인터페이스를 구현해야 한다.


**서블릿 필터 vs 인터셉터**

서블릿 필터는 단순하게 `doFilter`만 제공 된다.

인터셉터는 컨트롤러 호출 전(`preHandle`), 컨트롤러 호출 후(`postHandle`) , 요청 완료 이후(`afterCompletion`)으로

단계적으로 세분화 되어 있다.

---

#### 스프링 인터셉터 호출 흐름

1. `HTTP` 요청 이후 `WAS` , `Filter` 를 거쳐서 `DispatchServlet` 으로 들어온다.

2. `preHandle` 호출 후 `Handler Adapter`, `Controller` 를 호출한다.

3. `DispatchServlet`이 `View` 를 받아낸 후 `postHandle` 을 호출한다.

4. `View` 를 렌더링 한 후에 `afterCompletion` 을 호출한다.


**postHandle은 정확하게는 `HandlerAdapter` 이후 호출된다.**

