타임리프는 주로 HTML 태그에 `th:*` 속성을 지정하는 방식으로 동작한다.

해당 속성은 태그에 기존속성을 대체해서 사용된다

만약, 그 태그에 기존 속성이 작성되어 있지않다면 새로 만든다.

```html
<input type="text" name="mock" th:name="userA">   =>  <input type="text" name="userA">

<input type="text" class="text" th:attrappend="class='large '">  =>  <input type="text" class="large text">
<input type="text" class="text" th:attrprepend="class=' large'">  => <input type="text" class="large text">
<!-- 자동으로 적당한 위치에 class를 추가 -->
<input type="text" class="text" th:classappend="large">  => <input type="text" class="large text">

```




