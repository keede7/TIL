## Validator

_복잡한 검증로직을 분리하자_

컨트롤러에서 검증 로직이 차지하는 부분은 매우 크다. 이런 경우 별도의 클래스로 역할을 분리하는 것이 좋다. 

그리고 이렇게 분리한 검증 로직을 재사용 할 수도 있다.


### Validator 인터페이스


스프링은 검증을 체계적으로 제공하기 위해서 이 인터페이스들을 제공한다

```java
public interface Validator {
  boolean supports(Class<?> clazz);
  void validate(Object target, Errors errors);
}
```

+ `supports()` : 해당 검증기를 지원하는 여부 확인

+ `validate(Object target, Errors errors)` : 검증 대상 객체와 `BindingResult`


#### 사용 예시


```java
@Component
public class ItemValidator implements Validator {

  @Override
   public boolean supports(Class<?> clazz) {
     return Item.class.isAssignableFrom(clazz);
   }
  
  @Override
   public void validate(Object target, Errors errors) {
    Item item = (Item) target; // 검증 대상
    
    // 검증 로직 작성

   }
}

```

**컨트롤러**

```java
  private final ItemValidator itemValidator;
  
  @PostMapping("")
  public String add(@ModelAttribute Item item, BindingResult bindingResult) {
  
    itemValidator.validate(item, bindingResult);

    if (bindingResult.hasErrors()) {
       log.info("errors={}", bindingResult);
       return "validation/v2/addForm";
    }

    // 성공 로직 
  }

```


스프링이 Validator 인터페이스를 별도로 제공하는 이유는 체계적으로 검증 기능을 도입하기 위해서다. 

그런데 위에서는 검증기를 직접 불러서 사용했고, 다음과 같이 사용해도 된다.

```java
// ItemController 
  @InitBinder
  public void init(WebDataBinder dataBinder) {
     log.info("init binder {}", dataBinder);
     dataBinder.addValidators(itemValidator);
  }
  
  @PostMapping("")
  public String add(@Validated @ModelAttribute Item item, BindingResult bindingResult) {
  
   
   // itemValidator.validate(item, bindingResult);  // 삭제


    if (bindingResult.hasErrors()) {
       log.info("errors={}", bindingResult);
       return "validation/v2/addForm";
    }

    // 성공 로직 
  }
  
```


#### 동작 방식

`@Validated` 는 검증기를 실행하라는 애노테이션이다.

이 애노테이션이 붙으면 앞서 `WebDataBinder` 에 등록한 검증기를 찾아서 실행한다. 

그런데 여러 검증기를 등록한다면 그 중에 어떤 검증기가 실행되어야 할지 구분이 필요하다.

이때 `supports()` 가 사용된다. 여기서는 `supports(Item.class)`호출되고, 

결과가 true 이므로 `ItemValidator` 의 `validate()` 가 호출된다.
