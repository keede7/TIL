
### ArgumentResolver

조금 더 쉽게 로그인 회원을 찾아본다.


`@Login` 커스텀 애노테이션을 추가한다.

```java
@GetMapping("/")
public String homeLoginV3ArgumentResolver(@Login Member loginMember, Model model) {

   //세션에 회원 데이터가 없으면 home
   if (loginMember == null) {
   return "home";
   }
   
   //세션이 유지되면 로그인으로 이동
   model.addAttribute("member", loginMember);
   return "loginHome";
}
```

파라미터에 붙은 `@Login` 애노테이션을 통해서 직접 `ArgumentResolver` 를 구현하여 

동작하도록 설정하자


#### Login

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface Login {
}
```


#### LoginMemberArgumentResolver

```java
@Slf4j
public class LoginMemberArgumentResolver implements HandlerMethodArgumentResolver {
   @Override
   public boolean supportsParameter(MethodParameter parameter) {
   
       log.info("supportsParameter 실행");
       boolean hasLoginAnnotation =
           parameter.hasParameterAnnotation(Login.class);
       boolean hasMemberType =
          Member.class.isAssignableFrom(parameter.getParameterType());
           return hasLoginAnnotation && hasMemberType;
   }
   @Override
   public Object resolveArgument(MethodParameter parameter,
  ModelAndViewContainer mavContainer, NativeWebRequest webRequest,
  WebDataBinderFactory binderFactory) throws Exception {
       log.info("resolveArgument 실행");
       HttpServletRequest request = (HttpServletRequest)
      webRequest.getNativeRequest();
       HttpSession session = request.getSession(false);
       if (session == null) {
       return null;
       }
       return session.getAttribute(SessionConst.LOGIN_MEMBER);
   }
}
```



`supportsParameter()` : `@Login` 애노테이션이 있으면서 `Member` 타입이면 해당 `ArgumentResolver` 가 사용된다.

`resolveArgument() ` : 컨트롤러 호출 직전에 호출 되어서 필요한 파라미터 정보를 생성해준다. 

여기서는 세션에 있는 로그인 회원 정보인 `member` 객체를 찾아서 반환해준다. 

이후 스프링MVC는 컨트롤러의 메서드를 호출하면서 여기에서 반환된 `member` 객체를 파라미터에 전달해준다


---

### Config 로 적용


#### WebConfig

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
   @Override
   public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
    resolvers.add(new LoginMemberArgumentResolver());
   }
 //...
}
```





