```html

<h1>if, unless</h1>

<table border="1">
 <tr>
     <th>count</th>
     <th>username</th>
     <th>age</th>
 </tr>
 <tr th:each="user, userStat : ${users}">
     
     <td th:text="${userStat.count}">1</td>
     <td th:text="${user.username}">username</td>
     <td>
       
     <span th:text="${user.age}">0</span>
     <span th:text="'미성년자'" th:if="${user.age lt 20}"></span>
     <span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>
     </td>
 </tr>
</table>
  
<table border="1">
   <tr>
     <th>count</th>
     <th>username</th>
     <th>age</th>
   </tr>
   <tr th:each="user, userStat : ${users}">
     <td th:text="${userStat.count}">1</td>
     <td th:text="${user.username}">username</td>
     <td th:switch="${user.age}">
     <span th:case="10">10살</span>
     <span th:case="20">20살</span>
     <span th:case="*">기타</span>
     </td>
   </tr>
</table>

```

if, unless

타임리프는 해당 조건이 맞지 않으면 태그 자체를 렌더링 하지 않는다.

만약 다음 조건이 false 인 경우 <span>...<span> 부분 자체가 렌더링 되지 않고 사라진다.

switch

`*`는 만족하는 조건이 없을때 사용하는 default

```jsx
<table border="1">
  <tr>
      <th>count</th>
      <th>username</th>
      <th>age</th>
  </tr>
  
  <tr th:each="user, userStat : ${users}">
      <td th:text="${userStat.count}">1</td>
      <td th:text="${user.username}">username</td>
      
      <td>
        <span th:text="${user.age}">0</span>
        <span th:text="'미성년자'" th:if="${user.age lt 20}"></span>
        <span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>
      </td>
  </tr>
</table>

<table border="1">
  
  <tr>
    <th>count</th>
    <th>username</th>
    <th>age</th>
  </tr>

  <tr th:each="user, userStat : ${users}">
    
      <td th:text="${userStat.count}"></td>
      <td th:text="${user.username}">username</td>
      <td th:switch="${user.age}">
          <span th:case="10">10t</span>
          <span th:case="20">20t</span>
          <span th:case="*">기타</span>
      </td>
  </tr>    
</table>
```
