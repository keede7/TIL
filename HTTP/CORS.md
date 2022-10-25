### CORS ( `Cross-Origin Resource Sharing` )

서로 다른 `Domain` 간의 통신을 `교차 출처` 라고하는데, 서로 다른 출처 간에 `Resource` 를 공유하는 것은

보안적인 측면에서 허용되지 않는다.

`HTTP Header` 를 이용해서 웹 애플리케이션에서 다른 출처의 자원에 접근 할 수 있는 권한을 부여하도록

브라우저에 알려주는 체제가 `CORS` 이다.

---

`HTML` 태그 는 무관하며, 특히 `JavaScript` 에서 교차 출처 요청이 이루어질 때 제약이 걸린다.

이러한 경우 `HTTP` 요청 헤더에 값을 지정해 줘야 하고, 브라우저는 스스로 `HTTP` 교차 출처 요청을 진행한다.

기본적으로 `Domain` 이 다르면 그것을 브라우저가 인식해서 `Origin` 필드에 요청 출처를 담아서 `HTTP` 교차 출처 요청을 진행하는 것.


---

출처를 비교하는 로직은 `Server` 에서 구현된 게 아니라 브라우저 내에 정의된 스펙이다.

브라우저는 `Client` 의 요청헤더 와 `Server` 의 응답 헤더를 스스로 비교해서 최종 응답을 처리한다.

> 두개의 출처를 비교하는 방법

1. `Protocol`
2. `Host`
3. `Port`

위 3가지만 동일하면 가능하며, 이외의 속성은 달라도 상관없다.

---

### CORS 요청의 종류

#### Simple Request

`Preflight Request` 라 불리는 `예비 요청` 없이 `본 요청` 만 진행 하고, `Server` 가 응답 헤더에 `Access-Control-Allow-Origin`와 같은 

값을 전송하면 브라우저가 서로 비교한 후 `CORS` 정책 위반 여부를 검사한다.


> 간단한 만큼 제약사항이 있다.

1. `Get` , `Post` `Head` 중 한 가지 메서드를 사용해야 한다.
2. 헤더는 `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` , `DPR` , `Downlink` , `Save-Data` , `Viewport-Width Width` 만 가능하고, `Custom Header`는 허용하지 않는다.
3. `Content-Type` 은 `application/x-www-urlencoded` , `multipart/form-data` , `text/plain` 만 가능하다.

만약 해당 제약사항에 위반 될 경우에는 `예비 요청` 방식으로 전송이 된다.

#### Preflight Request

브라우저가 한번에 요청을 보내지 않고, `예비 요청` 과 `본 요청` 으로 나누어서 서버에 전달한다.

브라우저가 `예비 요청` 을 보내는 것을 `Preflight Request` 라고 하며, `OPTIONS` 라는 `HTTP Method` 가 사용된다.

마찬가지로 `Server`의 응답헤더, `Client` 의 요청헤더를 비교해서 브라우저가 그 최종응답을 전달한다.


---

`예비 요청` 은 다른 서버에 출처요청 했을 때, 진행 할 수 있는지 없는지 여부를 판단하는 작업이라고 생각하면 될 것 같다.





