### Embedded Type

새로운 값 타입을 직접 정의해서 사용할 수 있는데, `JPA` 에서 이것을 `임베디드 타입` 이라고 한다.

중요한 것은 직접 정의한 임베디드 타입도 `int`, `String`, 처럼 값 타입이라는 것이다.

ex) 회원 엔티티는 이름, 근무기간, 집 주소 를 가진다.

```java
public class Member {
	@Id
	@GeneratedValue
	private Long id;

	private String name;
	
	// 근무기간
	@Temporal(TemporalType.DATE) Date startDate;
	@Temporal(TemporalType.DATE) Date endDate;

	//주소
	private String city;
	private String street;
	private String zipcode;
}
```

회원이 상세한 데이터를 그대로 가지고 있는 것은 객체지향적이지 않다.

대신 근무기간, 주소와 같은 타입이 있다면 좀 더 명확해질 것.

```java
@Id @GeneratedValue
private Long id;
private String name;

@Embedded Period workPeriod;
@Embedded Address homeAddress;
```

```java
@Embeddable
public class Period {
		@Temporal(TemporalType.DATE) Date startDate;
	@Temporal(TemporalType.DATE) Date endDate;

	// 값 타입을 위한 메서드 정의 가능.	
}
```

```java
@Embeddable
public class Address {
	@Column(name="city") // 매핑할 컬럼 정의 가능
	private String city;
	private String street;
	private String zipcode;
}
```

새로 정의한 값 타입들은 재사용가능하고 응집도도 높다.

임베디드 타입을 사용하기 위해서는 2가지 어노테이션이 필요하다.

- `@Embeddable` : 값 타입을 정의한느 곳에 표시
- `@Embedded` : 값 타입을 사용하는 곳에 표시.

임베디드 타입은 기본 생성자가 필수다.

임베디드 타입을 포함한 모든 값을 엔티티 생명주기에 의존하므로 

엔티티와 임베디드 타입의 관계를 UML로 표현하면 `컴포지션 관계` 가 된다.

임베디드 타입 덕분에 객체와 테이블을 세밀하게 매핑하는 것이 가능하다.

잘 설계한 ORM 애플리케이션은 매핑한 테이블 수 보다 클래스의 수가 더 많다.

---

2-1 임베디드 타입과 연관관계

임베디드 타입은 `값` 타입을 포함하거나 엔티티를 참조할 수 있다.

---

2-2 `@AttributeOverride` : 속성 재정의

임베디드 타입에 정의한 매핑정보를 재정의하려면 `@AttributeOverride`를 사용하면 된다.

만약 같은 임베디드타입을 2개 쓰려고한다면?

```java
public class Member {

	@Embedded
	Address homeAddress;

	@Embedded
	@AttributeOverrides({
		@AttributeOverride(name="city", column = @Column(name ="Company_city"),
@AttributeOverride(name="street", column = @Column(name ="Company_street"),
@AttributeOverride(name="zipcode", column = @Column(name ="Company_zipcode")
	)}
	Address companyAddress;
}
```


`@AttributeOverride` 는 많이 쓰면 코드가 지저분해진다.

다행히도, 이 한 엔티티에 같은 임베디드  타입을 중복해서 사용하는 일은 많지 않다.

임베디드 타입이 `null` 이면 매핑한 컬럼 값 모두 `null` 처리가 된다.
