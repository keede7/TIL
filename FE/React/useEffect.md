### useEffect 

`React` 는 `Component` 의 `State` 가 변경이 되어 리렌더링 되거나,

특정 화면을 진입했을 경우 모든 `Component` 가 렌더링된다.


만약 우리가 `API` 를 호출 하기 위한 코드를 작성했을 경우 

해당 코드를 한번만 호출하고싶다면 어떤식으로 해야하는 가? 그떄 `useEffect` 를 사용해야 한다.


```jsx
useEffect(function, array);
```

* First Arg(Function) : 실행시키고자 하는 함수를 작성한다.

* Second Arg(Array) : 변경을 감지하고자 하는 상태값을 작성한다.


---

`useEffect` 를 통해서 `Component` 가 처음 렌더링을 했을 시점에 한번 호출하고 이후에 상태가 변경되어도

호출되지 않게 한다.

```jsx
 useEffect( () => {
      console.log("start log");
 }, []);
 
 // 결과 (1번 호출)
 start log
 ```
 
 
 
 만약 어떤 값의 상태가 변경되었을 경우 한정으로 코드를 호출하고 싶을 경우
 ```jsx
 const [counter, setCounter] = useState(0);
 
 useEffect( () => {
  console.log("modify counter");
 }, [counter]);
 ```
 
 ---
 
 ### Cleanup Function
 
 많이 쓰이는 방법은 아니다.
 
 특정한 `Component`가 화면으로부터 제거됐을 경우 호출하고 싶다면?
 
 ```jsx
 useEffect( () => {
  console.log("created");
  return () => console.log("destroyed");
 }, []);
```

`return` 을 통해서 함수를 작성해주면 동작한다.
