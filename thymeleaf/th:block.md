해당 태그는 타임리프의 유일한 태그 그 자체이다.

```html
<!-- 렌더링 결과에서는 사라지는 태그 -->
<th:block th:each="user, userStat : ${users}">
    <div>
      <span th:text="${user.username}"></span>
      <span th:text="${user.age}"></span>
    </div>
</th:block>
```

렌더링 결과

```html
    <div>
      <span>Mike</span>
      <span>30</span>
    </div>
    <div>
      <span>Jack</span>
      <span>15</span>
    </div>

```
