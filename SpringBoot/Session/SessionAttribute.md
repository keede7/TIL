
### SessionAttribute Annotation


스프링은 세션을 조금 더 편리하게 사용할 수 있도록 `@SessionAttribute`를 제공한다.

기존에 `HttpSession` 에서 로그인 된 사용자를 조회할 떄 다음과 같은 코드를 사용했다.

```java

// 메서드 일부
 HttpSession session = request.getSession(false);
 
 Member loginMember = (Member) session.getAttribute(key);

```

하지만 스프링에서 `@SessionAttribute` 를 통해 더 간결하게 작성할 수 있게 되었다


```java
@GetMapping("/")
public String homeLoginV3Spring(
                   @SessionAttribute(name = SessionConst.LOGIN_MEMBER, required = false)
                  Member loginMember, Model model) {
                  
   //세션에 회원 데이터가 없으면 home
   if (loginMember == null) {
   return "home";
   }
   
   //세션이 유지되면 로그인으로 이동
   model.addAttribute("member", loginMember);
   return "loginHome";
}


```


