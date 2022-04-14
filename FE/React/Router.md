### React-Router

`React` 개발을 하면서 `Spring MVC` 를 개발할 때 처럼 페이지를 이동 시킬 수 있다.

`React Router` 를 사용함으로써 특정한 **Component** 로 이동할 수 있다.

`Router` 는 2가지 종류가 있다.

1. Hash Router

2. Browser Router

여기서 `Hash Router` 는 잘 쓰지 않으며,  주로 사용하는 것이 `Browser Router` 이다.

> Hash Router의 예시

```url
http://localhost:3000/#/path
```

> Browser Router의 예시

```url
http://localhost:3000/path
```

---

#### 설치 방법

1. 터미널을 연다

2. `npm i react-router-dom` 을 입력

3. 버전을 입력할 경우 `npm i react-router-dom@5.3.0`

#### 사용 문법

기본적으로 다음과 같이 사용한다.

* React-Router-Dom 6.X 이전버전

```jsx
import { BrowserRouter as Router } from 'react-router-dom'

function App() {
  return (
      <Router>
        <Switch>
            <Route path="/movie">
            </Route>
         </Switch>
      </Router>
  )
}
```

* React-Router-Dom 6.X 이후버전

**`Switch` 가 `Routes` 로 대체되었다.**

```jsx
import { BrowserRouter as Router } from 'react-router-dom'

function App() {
  return (
      <Router>
        <Routes>
          <Route path="/" element={< Home / >} >
          </Route>
        </Routes>
      </Router>
  )
}
```

--- 

### 동적 URL ( useParams )

`Router` 는 다음과 같은 문법을 사용해서 동적으로 페이지를 변경할 수 있게 해준다.

```jsx
function App() {
  return (
      <Router>
        <Routes>
          <Route path="/movie/:id" element={< Home / >} >
          </Route>
        </Routes>
      </Router>
  )
}

<Link to={`/movie/${id}`}> {title} </Link> 

```

그 이후 해당 컴포넌트에서 `useParams` 를 통해 `id` 값을 추출할 수 있다.

여기서 `id` 는 `key` 값으로 쓰이며 `Route` 의 `:id` 값을 의미한다.

```jsx
import { useEffect } from "react";
import { useParams } from "react-router-dom";

function Detail() {
  const { id } = useParams();

    return <h1>Detail</h1>;
  }
  
export default Detail;
```
 
