### DynamicQuery

`MyBatis`는 `XML` 을 기반으로 유연한 동적 쿼리 작성 기능을 제공한다.

---

1. IF 문

사용예시는 다음과 같다.

**test** 안에 비교하고자 하는 조건식을 작성하면 된다.

`value` 값은 주로 `MyBatis` 의 `Query` 실행을 위해서 보내준 매개변수 값 `=Property` 를 가리킨다.

```xml
<if test="value == null"> 
  Query
</if>
```

+ Boolean 타입

`boolean` 타입의 `Wrapper Class` 이기 때문에 `boolean`타입 비교조건을 쓸수 있다.

기본값으로 `null`을 받으며 기본형(`boolean`) 일 경우 `false` 가 기본값이 된다.



```xml
<!-- true일 때 -->
<if test="value"></if>

<!-- true가 아닐 때 (null 또는 false) -->
<if test="!value"></if>

<!-- null일 때 -->
<if test="value == null"></if>

<!-- null이 아닐 때 (true 또는 false) -->
<if test="value != null"></if>
```


+ Integer 타입

`int` 타입의 `Wrapper Class`로 `int` 타입의 비교조건을 마찬가지로 사용할 수 있다.

기본값으로 `null`을 받으며 기본형(`boolean`) 일 경우 `false` 가 기본값이 된다.

```xml

<!-- 특정 값일 때 -->
<if test="value == 1"></if>

<!-- 특정 값보다 작을 때 -->
<if test="value lt 1"></if>
<if test="value < 1"></if>

<!-- 특정 값보다 작거나 같을 때 -->
<if test="value <= 1"></if>

<!-- 특정 값보다 클 때 -->
<if test="value gt 1"></if>
<if test="value > 1"></if>

<!-- 특정 값보다 크거나 같을 때 -->
<if test="value gte 1"></if>
<if test="value >= 1"></if>

```

+ String 타입

기본값은 `null` 이다.

```xml
<!-- 문자열 비교 -->
<if test='value.equals("obj")'></if>

<!-- null 비교 -->
<if test='value == null'></if>
<if test='value != null'></if>
```


