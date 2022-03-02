### JSX

`JSX` 는 `JavaScript` 를 확장한 문법이다.

`React` 요소를 만들어주며 `HTML`에서 사용한 문법과 흡사한 문법을 사용해서 만든다.

생긴게 HTML 과 비슷하기 때문에 `JSX` 로 `React` 요소를 만드는 것이 편하다.

**변환 전 코드**

(+ `React.createElement` 는 요즘 형식에는 맞지 않는 방식이다.)

```javascript
<script>

const root = document.getElementById("root");
const h3 = React.createElement("h3",
		{ id : "title",
			onMouseEnter : () => console.log("enter"),
		},
		"Hello"
);

const btn = React.createElement(
		"button",
		{
			onClick : () => console.log("im clicked"),
		},
		"button"
);

</script>
```

**변환 후 코드**

```javascript

// JSX
const Title = <h3 id="title" onMouseEnter={ () => console.log("im clicked")}> 
Hello </h3>

// 위와 아래가 같은 코드이다.
const h3 = React.createElement("h3",
		{ id : "title",
			onMouseEnter : () => console.log("enter"),
		},
		"Hello"
);
```



`h3` 태그 안에 `props`를 담는다. 생긴 모양은 `HTML`과 흡사하다.

`JSX` 덕분에 `React.createElement` 를 사용하지 않는다.

`React.createElement` 는 형식도 복잡하기 떄문에, 여러가지 것을 기억해서 사용을 해야하는 단점이 있다.


반면 `JSX`는 훨씬 이해하기 쉽고 `HTML`과 같은 형식이다.

하지만 브라우저가 온전히 `JSX` 를 이해하지는 못하기 떄문에 한번 변환이 필요함.

그떄 `Babel` 이라는 것을 사용해서 `JSX` 코드를 이해할 수 있게 한다.

