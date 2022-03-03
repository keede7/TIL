### State Function

```javascript

const [ counter, setCounter ] = React.useState();

// const onClick = () => setCounter(counter + 1);
function onClick() {
  setCounter(counter + 1);
}

```


`onClick` 메서드로 `counter` 의 값을 변경하는 이 방식은 `counter` 가 다른곳에서 `update` 될 수 있으므로

좋은 방식은 아니다, 사실 그런 현상은 자주 나오지는 않지만 `State` 를 바꾸는 방법이 2가지가 있다.


---

1.  `setCounter` 를 통해서 원하는 값을 넣어주는 방법.

새 값으로 변경하기 떄문에 한번 더 호출해도 변함이 없다.

```javascript
setCounter(200);
```


---


2.  이전 값을 이용해서 현재 값을 계산하는 방법

이 방법이 현재 위에서 쓰던 방법이다.


---

`setCounter(counter + 1)` 과 같은 결과물을 주긴 하지만 함수로 쓰는 방법이 조금 더 안전하다.

함수로 쓰게되면 `React` 가 현재 값을 확실하게 보장한다.

```javascript

setCounter((current) => counter + 1);

```
