타임리프는 자바스크립트에서 타임리프를 편하게 사용할 수 있는 자바스크립트 인라인 기능을

제공한다. 다음과 같이 사용한다

```jsx
<script th:inline="javascript">
```

```html
<!-- 자바스크립트 인라인 사용 전 -->
<script>
 var username = [[${user.username}]];
 var age = [[${user.age}]];
  
 //자바스크립트 내추럴 템플릿
 var username2 = /*[[${user.username}]]*/ "test username";
  
 //객체
 var user = [[${user}]];
</script>

<!-- 자바스크립트 인라인 사용 후 -->
<script th:inline="javascript">
 var username = [[${user.username}]];
 var age = [[${user.age}]];
 //자바스크립트 내추럴 템플릿
 var username2 = /*[[${user.username}]]*/ "test username";
 //객체
 var user = [[${user}]];
</script>
</body>
</html>
```

텍스트 렌더링

```jsx
var username = [[${user.username}]]
인라인 사용 전 - username = userA;
사용 후 - username = "userA";
```

인라인을 사용하지 않으면 변수명이  username 에 대입하는 형태가 되기 때문에 에러가난다.

인라인을 사용하면 렌더링 후 결과가 문자열이면 “를 붙여준다.

그리고 자바스크립트에서 문제가 될 수 있는 문자가 포함되면 이스케이프 처리도 한다.

객체

인라인 기능을 사용하면 객체를 `JSON`으로 자동변환해준다.

인라인 사용전에는 `toString()`을 호출한다

인라인 사용 후에는 `JSON`으로 변환해준다.

자바스크립트 인라인 each

```jsx
<script th:inline="javascript">

  [# th:each="user, userStat : ${users}"]
	var user[[${stat.count}]] = [[${user}]];
  [/]

</script>
```
