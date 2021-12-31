
타임리프에서 반복은 `th:each` 를 사용한다.

그리고 여러 상태값을 지원한다.

```jsx
<table border="1">

  <tr>
    <th>username</th>
    <th>age</th>

  </tr>
  <tr th:each="user : ${users}">
    <td th:text="${user.username}"> username</td>
    <td th:text="${user.age}"> age</td>
  </tr>
</table>

<table border="1">
  <tr>
    <th>count</th>
    <th>username</th>
    <th>age</th>
    <th>etc</th>
  </tr>

  <tr th:each="user, userStat : ${users}">
    <td th:text="${userStat.count}">username</td>
    <td th:text="${user.username}">username</td>
    <td th:text="${user.age}">username</td>
    <td>
      index = <span th:text="${userStat.index}"></span>
      count = <span th:text="${userStat.count}"></span>
      size = <span th:text="${userStat.size}"></span>
      even = <span th:text="${userStat.even}"></span>
      odd = <span th:text="${userStat.odd}"></span>
      first = <span th:text="${userStat.first}"></span>
      last = <span th:text="${userStat.last}"></span>
    </td>
    
  </tr>

</table>
```

`th:each` 는 List 뿐만 아니라 Iterable, Enumeration 을 구현한 모든 객체에 반복을 사용할 수 있다.

Map도 사용할수있는데 이 때 Map.Entry

반복 상태 유지

```jsx
th:each="user, userStat : ${users}"
```

여기서 두번째 파라미터는 생략이 가능한데

생략하면 지정한 변수명 (user) + (Stat)가 된다.

반복상태 유지기능

- index : 0부터 시작
- count : 1부터 시작
- size : 전체 사이즈
- even, odd : 홀수 짝수 여부 (boolean)
- first, last : 처음, 마지막 여부(boolean)
- current : 현재 객체
