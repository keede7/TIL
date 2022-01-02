### BindingResult

스프링이 제공하는 검증 오류를 보관하는 객체이다. 검증 오류가 발생하면 여기에 보관하면 된다.
`BindingResult` 가 있으면 `@ModelAttribute` 에 데이터 바인딩 시 오류가 발생해도 컨트롤러가 호출된다.

예) `@ModelAttribute`에 바인딩 시 타입 오류가 발생하면?

1) `BindingResult` 가 없으면 400 오류가 발생하면서 컨트롤러가 호출되지 않고, 오류 페이지로 이동한다.

2) `BindingResult` 가 있으면 오류 정보( `FieldError` )를 `BindingResult` 에 담아서 컨트롤러를 정상 호출한다.

`BindingResult`에 검증 오류를 적용하는 3가지 방법
+ `@ModelAttribute` 의 객체에 타입 오류 등으로 바인딩이 실패하는 경우 스프링이 `FieldError` 생성해서 `BindingResult` 에 넣어준다.

+ 개발자가 직접 넣어준다.

+ Validator 사용

사용예시

```java
@GetMapping("")
public String binding(@ModelAttribute Item item, BindingResult br) {
// ...
}
```

해당 메서드를 호출할 때 검증할 데이터 대상 다음으로 `BindingResult`를 작성해야 한다.

#### 필드오류

```jsx
if (!StringUtils.hasText(item.getItemName())) {
 bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수입니다."));
}
```

```jsx
public FieldError(String objectName, String field, String defaultMessage) {}
```

데이터 바인딩이 되는 값 중 특정 필드에 오류가 있으면 `FieldError` 객체를 생성해서 `bindingResult`에 담아 두면 된다

- `objectName` - @ModelAttribute 의 이름
- `field` - 오류가 발생한 필드 이름
- `defaultMessage` - 오류 기본 메세지

#### 글로벌 오류

```java
bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
```

```java
public ObjectError(String objectName, String defaultMessage) {}
```

특정 필드를 넘어서는 오류가 있으면 `ObjectError` 객체를 생성해서 `bindingResult` 에 담아두면 된다.
+ `objectName` : `@ModelAttribute` 의 이름
+ `defaultMessage` : 오류 기본 메시지


#### 타임리프 스프링 검증 오류 통합 기능

타임리프는 스프링의 `BindingResult` 를 활용해서 편리하게 검증 오류를 표현하는 기능을 제공한다.

`#fields` : `#fields` 로 `BindingResult` 가 제공하는 검증 오류에 접근할 수 있다.
`th:errors` : 해당 필드에 오류가 있는 경우에 태그를 출력한다. `th:if` 의 편의 버전이다.
`th:errorclass` : `th:field` 에서 지정한 필드에 오류가 있으면 `class` 정보를 추가한다


### FieldError 생성자

`FieldError` 는 두 가지 생성자를 제공하며, `ObjectError` 도 유사하게 두 가지 생성자를 제공한다. 

```java
  public FieldError(String objectName, String field, String defaultMessage);
  public FieldError(String objectName, String field, @Nullable Object rejectedValue, 
  boolean bindingFailure, @Nullable String[] codes, @Nullable Object[] arguments, @Nullable String defaultMessage)
```

##### 파라미터 목록
+ `objectName` : 오류가 발생한 객체 이름
+ `field` : 오류 필드
+ `rejectedValue` : 사용자가 입력한 값(거절된 값)
+ `bindingFailure` : 타입 오류 같은 바인딩 실패인지, 검증 실패인지 구분 값
+ `codes` : 메시지 코드
+ `arguments` : 메시지에서 사용하는 인자
+ `defaultMessage` : 기본 오류 메시지
+ 

##### 오류 발생시 사용자 입력 값 유지
```java
// public FieldError(String objectName, String field, @Nullable Object rejectedValue, boolean bindingFailure, 
// @Nullable String[] codes, @Nullable Object[] arguments, @Nullable String defaultMessage)

new FieldError("item", "price", item.getPrice(), false, null, null, "가격은 1,000 ~ 1,000,000 까지 허용합니다.")
```

사용자의 입력 데이터가 컨트롤러의 @ModelAttribute 에 바인딩되는 시점에 오류가 발생하면 모델
객체에 사용자 입력 값을 유지하기 어렵다. 예를 들어서 가격에 숫자가 아닌 문자가 입력된다면 가격은
Integer 타입이므로 문자를 보관할 수 있는 방법이 없다. 그래서 오류가 발생한 경우 사용자 입력 값을
보관하는 별도의 방법이 필요하다. 그리고 이렇게 보관한 사용자 입력 값을 검증 오류 발생시 화면에 다시
출력하면 된다.

`FieldError` 는 오류 발생시 사용자 입력 값을 저장하는 기능을 제공한다.
여기서 `rejectedValue` 가 바로 오류 발생시 사용자 입력 값을 저장하는 필드다.

##### 스프링의 바인딩 오류 처리
타입 오류로 바인딩에 실패하면 스프링은 `FieldError` 를 생성하면서 사용자가 입력한 값을 넣어둔다. 

그리고 해당 오류를 BindingResult 에 담아서 컨트롤러를 호출한다.

따라서 타입 오류 같은 바인딩 실패시에도 사용자의 오류 메시지를 정상 출력할 수 있다
