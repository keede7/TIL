### HttpSession



세션은 대부분의 웹 애플리케이션에 필요하다.

서블릿은 세션을 위해서 `HttpSession` 이라는 기능을 제공한다.

세션은 직접 구현해서 사용해도 되지만 `HttpSession` 을 이용해서 

조금 더 편리하게 세션을 만들 수 있게 도와준다.


---


서블릿을 통해서 `HttpSession` 을 생성하게 되면 `JESSIONID`를 **key** 로 가지는 랜덤값이 생성된다.


---


#### 사용 예시 ( 로그인 )

```java

@PostMapping("/login")
public String login(HttpServeletRequest request) {

  // 기존 세션 반환, 없으면 새로 생성
  HttpSession session = request.getSession();
  // 세션에 value 저장
  session.setAttribute(key, value);

}
```

세션을 사용하기 위해서는 다음 코드를 사용한다.

```java
request.getSession(true);
```

이 떄, 매개변수는 빈 값일 경우 기본적으로 `true` 가 적용된다.

만약 `true` 일 경우
+  세션이 있으면 기존의 세션을 반환
+  세션이 없을 경우 새로운 새션을 생성해서 반환한다.

만약 `false` 일 경우
+  세션이 있으면 기존의 세션을 반환
+  세션이 없을 경우 새로운 세션을 생성하지 않는다. **(null)**




---


#### 사용 예시 ( 로그아웃 )

```java
@PostMapping("/logout")
public String logout(HttpServletRequest request) {
 //세션을 삭제한다.
 HttpSession session = request.getSession(false);
 
 if (session != null) {
 session.invalidate(); // 세션을 제거한다.
 }
 return "redirect:/";
}
```


---

#### 사용 예시 ( Home Page )


```java
@GetMapping("/")
public String homeLoginV3(HttpServletRequest request, Model model) {
 //세션이 없으면 home
 HttpSession session = request.getSession(false);
 if (session == null) {
 return "home";
 }
 Member loginMember = (Member)
session.getAttribute(SessionConst.LOGIN_MEMBER);
 //세션에 회원 데이터가 없으면 home
 if (loginMember == null) {
 return "home";
 }
 //세션이 유지되면 로그인으로 이동
 model.addAttribute("member", loginMember);
 return "loginHome";
}
```

홈페이지에 들어왔을 경우에는 로그인을 하지 않는 사용자의 세션생성이 만들어 질 수 있기때문에

**`request.getSession(false)`** 를 사용한다.


