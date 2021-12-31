
타임리프에서 URL을 생성할 떄는 `@{...}` 을 사용한다.

기본적으로 4가지 방식으로 조합이 가능하다.

1. 단순한 URL 

```
<a th:href="@{/hello}"></a>
```

단순하게 해당 `<a>` 태그로 만들어진 링크를 클릭하면 다음과 같이 이동한다

결과  ->  `localhost:8080/hello`


2. Query Parameter

```
<a th:href="@{/hello(param1=${data1}, param2=${data2})}"></a>
```

해당 링크를 클릭하면 마치 `Query Paramter` 를 사용하는 것 처럼 다음과 같이 이동한다.

결과  ->  `localhost:8080/hello?param=data1&param2=data2`


3. REST

```
<a th:href="@{/hello/{param1}/{param2}(param1=${data1}, param2=${data2})"></a>
```

주로 REST API 방식으로 설계 했을 경우 사용한다.

결과  ->  `localhost:8080/hello/data1/data2`



4. Query Parameter + Rest

경로변수와 쿼리 파라미터를 같이 사용하는 경우도 있다.

```
<a th:href="@{/hello/{param1}(param1=${data1}, param2=${data2})"></a>
```

결과  ->  `localhost:8080/hello/data1?param2=data2`

