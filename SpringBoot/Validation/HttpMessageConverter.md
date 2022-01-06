### Validation, HttpMessageConverter

HttpMessageConverter는 컨트롤러에서 요청받은 매개변수의 타입에 따라서 특정한 타입으로 변환시켜주는 역할을 한다.

`@Valid`, `@Validated` 는 `HttpMessageConverter`에 적용이 가능하다 (`ModelAttribute`, `RequestBody`)

+ `@ModelAttribute` 는 HTTP 요청 파라미터(URL 쿼리 스트링, POST Form)를 다룰 때 사용한다.

+ `@RequestBody` 는 HTTP Body의 데이터를 객체로 변환할 때 사용한다. 주로 API JSON 요청을 다룰 때 사용한다.
 



`@ModelAttribute` vs `@RequestBody`

HTTP 요청 파리미터를 처리하는 `@ModelAttribute` 는 각각의 필드 단위로 세밀하게 적용된다. 

그래서 특정 필드에 타입이 맞지 않는 오류가 발생해도 나머지 필드는 정상 처리할 수 있었다.

`HttpMessageConverter` 는 `@ModelAttribute` 와 다르게 각각의 필드 단위로 적용되는 것이 아니라, 전체 객체 단위로 적용된다.

따라서 메시지 컨버터의 작동이 성공해서 매핑할 객체를 만들어야 `@Valid` , `@Validated` 가 적용된다.

`@ModelAttribute` 는 필드 단위로 정교하게 바인딩이 적용된다. 

특정 필드가 바인딩 되지 않아도 나머지 필드는 정상 바인딩 되고, `Validator`를 사용한 검증도 적용할 수 있다.

`@RequestBody` 는 `HttpMessageConverter` 단계에서 JSON 데이터를 객체로 변경하지 못하면 이후 단계 자체가 진행되지 않고 예외가 발생한다.

컨트롤러도 호출되지 않고, Validator도 적용할 수 없다.
