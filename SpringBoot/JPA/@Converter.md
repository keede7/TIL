
### @Converter

`Converter` 를 사용하면 `Entity`의 데이터를 변환해서 DB에 저장할 수 있다.

예를 틀어 회원의 VIP 여부를 `boolean` 타입을 사용하고 싶다면 

`JPA` 를 사용했을 때 방언에 따라서 다르겠지만 DB에는 0 또는 1인 숫자로 저장된다. 

그러나 문자열로 저장하고 싶다면 `Converter`를 사용하면 된다.

```java
@Convert(converter = BooleanToYNConverter.class)
private boolean vip;
```

`@Converter`를 지정해서 DB에 저장되기 직전에 `BooleanToYNConverter` 컨버터가 동작하도록 했다.

```java
@Converter
public class BooleanToYNConveter implements AttributeConverter<Boolean, String> {

	@Override
	public String convertToDatabaseColumn(Boolean attribute) {
		return (attribute != null && attribute ) ? "Y" : "N";
	}
	
	@Override 
	public Boolean convertToEntityAttribute(String dbData) {
		return "Y".equals(dbData);
	}
}
```

`<Boolean, String>` 을 지정해서 `boolean` 을 `String` 으로 변환한다.

`AttributeConverter` 인터페이스에서 구현할 메서드는 2개 이다.

- `convertToDatabaseColumn()`: 엔티티의 데이터를 DB 컬럼에 저장할 데이터로 변환한다.
- `convertToEntityAttribute()` : DB에서 조회한 컬럼 데이터를 엔티티 데이터로 변환한다.

`Converter` 는 클래스 레벨에도 설정할 수 있다.

이때는 `attributeName` 속성으로 어떤 필드에 적용할지 명시해야 한다

```java
@Convert(converter = BooleanToYNConverter.class, attributeName =" vip")
public class Member {
	// ...
	private boolean vip;
}
```

### 컨버터 글로벌 설정

모든 Boolean 타입에 컨버터를 적용하려면 `@Converter(autoApply = true)` 옵션을 적용하면 된다

```java
@Converter(autoApply = true)
public class BooleanToYNConverter implements 
		AttributeConverter<Boolean, String> {

		@Override
	public String convertToDatabaseColumn(Boolean attribute) {
		return (attribute != null && attribute ) ? "Y" : "N";
	}
	
	@Override 
	public Boolean convertToEntityAttribute(String dbData) {
		return "Y".equals(dbData);
	}

}
```

글로벌 설정을하면 따로 `Entity` 클래스에 적용하지 않아도 설정된다.

