# 배열 반복

`JS` 배열 객체의 내장 함수인 `map` 함수를 사용해서 반복되는 컴포넌트를 렌더링할 수 있다.

`map` 함수는 파라미터로 전달된 함수를 사용해서 배열 내 각 요소를 원하는 규칙에 따라 변환한 후 그 결과로 새로운 배열을 생성한다.

1. 문법

`arr.map(callback, [thisArg])`

이 함수의 파라미터는 다음과 같다.

- callback : 새로운 배열의 요소를 생성하는 함수
    - currentValue : 현재 처리하고 있는 요소
    - index : 현재 처리하고 있는 요소의 index
    - array : 현재 처리하고 있는 원본 배열
- thisArg : callback 함수 내부에서 사용할 this 레퍼런스

2. key 설정

```jsx
Warning: Each child in a list should have a unique "key" prop
```

`React` 에서 `key` 는 컴포넌트 배열을 렌더링 했을 때 어떤 원소에 변동이 있었는지 알아내려고 사용한다. `key` 가 없을 때 가상 DOM을 비교하는 과정에서 리스트를 순차적으로 비교하면서 변화를 감지한다.

다음과 같이 `map` 함수의 인자로 전달되는 함수 내부에서 컴포넌트의 `props` 를 전달하듯 설정하면 된다.

```jsx
const articleList = articles.map(article => {
	<Article key={article.id}>
		</Article>
});
```

하지만 위처럼 객체에 고유한 값이 없을  경우에는 `index` 라는 파라미터를 추가로 받아서 전달할 수 있다.

```jsx
const IterationSample = () => {
  const names = ["눈사람", "얼음", "눈", "바람"];
  const nameList = names.map((name, index) =>
 <li key={index}>{name}</li>);

  return <ul>{nameList}</ul>;
};
```

3. 데이터 추가 기능

```jsx
const [names, setNames] = useState([
    { id: 1, text: "눈사람" },
    { id: 2, text: "얼음" },
    { id: 3, text: "눈" },
    { id: 4, text: "바람" },
  ]);
const onClick = () => {
    // push =기존 배열의 변경, concat = 새로운 배열
  const nextNames = names.concat({
    id: nextId,
    text: inputText,
  });
};
```

배열의 새 항목을 추가할 때 `push` 대신 `concat` 을 사용한 이유는 `push` 함수는 기존 배열 자체를 변경하지만, `concat` 은 새로운 배열을 만들어 주기 떄문이다.

`React` 에서 상태를 업데이트 할 떄 기존 상태를 유지하고 새로운 값을 상태로 설정해야하는데 이를 `불변성 유지` 라고 한다.

`불변성` 을 유지해야 나중에 `React` 컴포넌트 성능을 최적화 할 수 있다.

4.. 데이터 제거 기능

데이터를 제거할 떄도 마찬가지로 불변성을 유지해줘야 하는데

특정 항목을 지울 때 `filter` 를 사용하게 된다.

특정 조건을 사용해서 해당하는 조건의 원소만 분류할 수 있다.


```jsx
const number = [1,2,3,4,5,6]
const bitggerThenThree = numbers.filter( number => number > 3);
```

