## 스프링 JdbcTemplate

`SQL` 을 직접 사용하는 경우 스프링이 제공하는 `JDBCTemplate`는 좋은 선택지이다. 

`JDBCTemplate`은 `JDBC`를 매우 편리하게 사용할 수 있게 도와준다.

**장점**

- 설정의 편리함
    - `JDBCTemplate`은 `spring-jdbc` 라이브러리에 포함되어 있는데, 이 라이브러리는 스프링으로 `JDBC`를 사용할 때 기본으로 사용되는 라이브러리이다. 그리고 별도의 복잡한 설정 없이 바로 사용할 수 있다.
- 반복 문제 해결
    - `JDBCTemplate` 은 **템플릿 콜백 패턴**을 사용해서, `JDBC` 를 직접 사용할 때 발생하는 대부분의 반복 작업을 대신 처리해준다.
    - 개발자는 `SQL` 작성하고, 전달할 파라미터를 정의하고, 응답 값을 매핑하기만 하면 된다.
    - 우리가 생각할 수 있는 대부분의 반복 작업을 대신 처리해준다,.
        - 커넥션 획득
        - `Statement` 준비 및 실행
        - 결과를 반복하도록 루프실행
        - 커넥션 종료, `Statement` ,  `ResultSet` 종료
        - 트랜잭션을 다루기 위한 커넥션 동기화
        - 예외 발생시 스프링 예외 변환기 실행

**단점**

- 동적 `SQL` 을 해결하기 어렵다.

**의존성 추가 코드**

```java
//JdbcTemplate 추가
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
```

### JdbcTemplate적용1  - 기본


**save()**

- `template.update()` : 데이터를 변경할 때 `update()` 를 사용하면 된다.
    - `Insert` , `update` , `delete` SQL에 사용한다.
    - `template.updatE()` 의 반환값은 `int` 인데, 영향 받은 로우 수를 반환.
- 데이터 저장시 `PK` 생성을 자동증가 방식을 사용하므로, `PK` 값인 `Id` 값을 개발자가 직접 지정하는 게 아니라 비워두고 저장해야 한다.
- KeyHolder`와 `connection.preparteStatement(sql, new String[] {"id"})` 를 사용해서 `id` 를 지정하면 `insert` 이후 데이터베이스에서 생성된 `id` 값을 조회할 수 있다.

**findById()**

- `template.queryForObject()`
    - 결과 로우가 하나일 때 사용한다.
    - `RowMapper`는 DB 반환 결과인 `ResultSet`을 객체로 변환한다.
    - 결과가 없으면 `EmptyResultDataAccessException`
    - 둘 이상이면 `IncorrectResultSizeDataAccessException`
- 결과가 없을 때 `Optional` 을 반환해야 한다. 따라서 결과가 없으면 `Optional.empty`를 대신반환하면 된다.

**findAll()**

- `template.query()`
    - 결과가 하나 이상일 떄 사용한다.
    - `RowMapper`는 DB의 반환 결과인 `ResultSet`을 객체로 변환한다.
    - 결과가 없으면 빈 컬렉션을 반환한다.
    
```java
private RowMapper<Item> itemRowMapper() {
   return (rs, rowNum) -> {
   Item item = new Item();
   item.setId(rs.getLong("id"));
   item.setItemName(rs.getString("item_name"));
   item.setPrice(rs.getInt("price"));
   item.setQuantity(rs.getInt("quantity"));
   return item;
 };
```


**ItemRowMapper()**

Db의 조회 결과를 객체로 변환할 떄 사용한다. `JDBC`를 직접 쓸  때 `ResultSet`를 사용햇던 부분을 떠올리면 된다.

차이가 있다면 `JdbcTemplate`이 루프를 돌려주며, 개발자는 `RowMapper`를 구현해서 내부 코드만 채운다고 생각하면 된다

```java
while ( resultSet이 끝날 떄 까지) {
	rowMapper(rs , rowNum);
}
```

### 적용2 - 동적 쿼리 문제

결과를 검색하는 `findAll()` 에서 어려운 부분은 사용자가 검색하는 값에 따라서 `SQL` 이 동적으로 달라야 한다는 점이다.

검색조건이 없음

```java
select id, item_name, price, quantity from item
```

상품명으로 검색

```java
select id, item_name, price, quantity from item
where item_name like concat('%',?,'%')
```

최대 가격으로 검색

```java
select id, item_name, price, quantity from item
where price <= ?
```

상품명, 최대가격으로 둘다 검색

```java
select id, item_name, price, quantity from item
where item_name like concat('%',?,'%')
 and price <= ?
```

결과적으로 4가지 상황에 따른 동적 `SQL`을 만들어서 실행해야 한다. 언뜻 보기에는 쉬워보이지만 , 막상 개발할때는 여러가지 상황에 대해서 고민을 해야한다. 

참고로 이후에 설명할 `MyBatis` 의 큰 장점으로 `SQL` 을 동적쿼리로 만들어서 쉽게 사용할수 있다는 점이다.

---

### 이름 지정 파라미터1

`JdbcTemplate` 은 기본으로 사용하면 순서대로 파라미터를 바인딩 한다.

```java
String sql = "update item set item_name=?, price=?, quantity=? where id=?";
template.update(sql,
 itemName,
 price,
 quantity,
 itemId);
```

여기서 값들을 순서대로 바인딩 된다.

대신 여기서 순서가 변경되면 문제가 발생한다.

```java
String sql = "update item set item_name=?, quantity=?, price=? where id=?";
template.update(sql,
 itemName,
 price,
 quantity,
 itemId)
```

결과적으로 `price` 와 `quantity` 가 바뀌는 문제가 발생하게 된다. 실무에서는 파라미터가 몇십개가 될 수 있어서 충분히 발생할수 있는 문제이다.

**개발을 할 때는 코드를 몇줄 줄이는 편리함도 중요하지만, 모호함을 제거해서 코드를 명확하게 만드는 것이 유지보수 관점에서 매우 중요하다.**

**이름 지정 바인딩**

`JdbcTemplate` 은 이런 문제를 보완하기 위해서 `NamedParameterJdbcTemplate`이라는 이름을 지정해서 파라미터를 바인딩하는 기능을 제공한다.

```java
 public Item save(Item item) {
   String sql = "insert into item (item_name, price, quantity) " +
   "values (:itemName, :price, :quantity)";
   
   SqlParameterSource param = new BeanPropertySqlParameterSource(item);
   KeyHolder keyHolder = new GeneratedKeyHolder();
   template.update(sql, param, keyHolder);
   
   Long key = keyHolder.getKey().longValue();
   item.setId(key);
   return item;
 }
```


**save()**

`SQL` 에서 다음과 같이 `?` 대신에 `:파라미터이름` 을 받는 것을 볼수 있다.

```java
insert into item (item_name, price, quantity) " +
 "values (:itemName, :price, :quantity)"
```

추가로 `NamedParameterJdbcTemplate`은 DB가 생성해주는 키를 매우 쉽게 조회하는 기능도 제공해준다.

### 이름 지정 파라미터 2

파라미터를 전달하려면 `Map` 처럼 `key` , `value` 데이터 구조를 만들어서 전달해야 한다. 여기서 `key`는 `:파라미터이름` 으로 지정한, 파라미터이름이고, `value`는 해당 파라미터의 값이 된다.

이름 지정 바인딩에서 자주 사용하는 파라미터는 3가지 종류가 있다.

- Map
- SqlParamterSource
    - MapSqlParameterSource
    - BeanPropertySqlParameterSource

1. **Map**

```java
Map<String, Object> param = Map.of("id", id);
Item item = template.queryForObject(sql, param, itemRowMapper())
```

2. **MapSqlParameterSource**

`Map` 과 유사한데, `SQL` 타입을 지정할 수 있는 `SQL` 에 좀더 특화된 기능을 제공한다. `SqlParameterSource` 인터페이스의 구현체이다.

`MapSqlParameterSource` 는 메서드 체인을 통해 편리한 사용법도 제공한다.

```java
  SqlParameterSource param = new MapSqlParameterSource()
   .addValue("itemName", updateParam.getItemName())
   .addValue("price", updateParam.getPrice())
   .addValue("quantity", updateParam.getQuantity())
   .addValue("id", itemId); //이 부분이 별도로 필요하다.
  template.update(sql, param)
```

3. **BeanPropertySqlParameterSource**

자바빈 프로퍼티 규약을 통해서 자동으로 파라미터 객체를 생성한다.

예로 `getItemName()` , `getPrice()` 가 있으면 다음과 같은 데이터를 자동으로 만들어 낸다.

- `key = itemName` , `value = 상품명 값`
- `key= price` , `value = 가격 값`

`SqlParameterSource` 인터페이스의 구현체이다.

```java
  // Item 프로퍼티에 맞게 바인딩해준다.
  SqlParameterSource param = new BeanPropertySqlParameterSource(item);
  KeyHolder keyHolder = new GeneratedKeyHolder();
  template.update(sql, param, keyHolder)
```

- 여기서 보면 `BeanPropertySqlParameterSource` 가 많은 것을 자동화해주므로 가장 좋아보이지만 항상 쓸 수 있는 것은 아니다.
- 예를 들어 수정을 할 경우에 다른 `DTO` 를 사용할 경우 `Property` 개수가 맞지 않아서 사용할 수 없게 된다

**BeanPropertyRowMapper**

**기존 코드**

```java
private RowMapper<Item> itemRowMapper() {
   return (rs, rowNum) -> {
     Item item = new Item();
     item.setId(rs.getLong("id"));
     item.setItemName(rs.getString("item_name"));
     item.setPrice(rs.getInt("price"));
     item.setQuantity(rs.getInt("quantity"));
     return item;
   };
}
```

**적용 코드**

```java
private RowMapper<Item> itemRowMapper() {
 return BeanPropertyRowMapper.newInstance(Item.class); //camel 변환 지원
}
```

`ResultSet` 의 결과를 받아서 자바빈 규약에 맞춰서 데이터를 변환한다.

**별칭**

그런데 `select item_name` 일 경우 `setItem_name()` 이라는 메서드가 없어서 쓸수가 없는데 이럴 떄는 별칭을 줘서 다음과 같이 쓰면 된다.

```java
select item_name as itemName
```

실제로 이 방법은 자주 사용된다. 특히 DB칼럼과 객체 이름이 완전히 다를 때 문제를 해결할 수 있다.

**관례의 불일치**

자바 객체는 카멜표기법을 사용한다. 반면 DB는 언더스코어를 사용하는 표기법을 사용하는데, 이 부분을 관례로 많이 쓰다보니 `BeanPropertyRowMapper`는 언더스코어 표기법을 카멜로 자동 변환해준다.

정리하자면 `snake_case` 는 자동으로 해결되니 그냥 두면 되고, 칼럼 이름과 객체이름이 완전히 다를 경우 조회 `SQL` 에서 **별칭**을 쓰면 된다.

### SimpleJdbcInsert

`JdbcTemplate`은 `Insert SQL`을 직접 작성하지 않아도 되도록 `SImpleJdbcInsert`라는 편리한 기능을 제공한다.

```java
// 생성자
public JdbcTemplateItemRepositoryV3(DataSource dataSource) {
   this.template = new NamedParameterJdbcTemplate(dataSource);
   this.jdbcInsert = new SimpleJdbcInsert(dataSource)
   .withTableName("item")
   .usingGeneratedKeyColumns("id");
  // .usingColumns("item_name", "price", "quantity"); //생략 가능
 }

public Item save(Item item) {
   SqlParameterSource param = new BeanPropertySqlParameterSource(item);
   Number key = jdbcInsert.executeAndReturnKey(param);
   item.setId(key.longValue());
   return item;
 }
```

**생성 코드 부분**

```java
this.jdbcInsert = new SimpleJdbcInsert(dataSource)
 .withTableName("item")
 .usingGeneratedKeyColumns("id");
// .usingColumns("item_name", "price", "quantity"); //생략 가능
```

- `withTableName` : 데이터를 저장할 테이블 명을 지정한다.
- `usingGeneratedKeyColumns` : `key` 를 생성하는 PK컬럼 명을 지정한다.
- `usingColumns` : Insert SQL 에 사용할 컬럼을 지정한다. 특정 값만 저장하고 싶을 때 사용한다. 생략 할 수 있다.

`SimpleJdbcInsert`는 생성 시점에 DB 테이블의 메타 데이터를 조회한다. 따라서 어떤 컬럼이 있는지 확인 할 수 있으므로 `usingColumns` 을 생략할 수 있다.

만약 특정 컬럼만 지정해서 저장하고 싶다면 `usingColumns` 를 사용하면 된다

**save()**

`jdbcInsert.executeAndReturnKey(param)` 을 사용해서 INSERT SQL 을 실행하고, 생성된 키 값도 매우 편리하게 조회할 수 있다.

```java
public Item save(Item item) {
 SqlParameterSource param = new BeanPropertySqlParameterSource(item);
 Number key = jdbcInsert.executeAndReturnKey(param);
 item.setId(key.longValue());
 return item;
}
```

### JdbcTemplate 기능 정리

주요기능

- `JdbcTemplate`
    - 순서 기반 파라미터 바인딩을 지원한다.
- `NamedParameterJdbcTemplate` (권장)
    - 이름 기반 파라미터 바인딩을 지원한다.
- `SimpleJdbcInsert`
    - Insert SQL을 편리하게 사용할 수 있다.
- `SimpleJdbcCall`
    - 스토어드 프로시저를 편리하게 호출할 수 있다.

**조회**

**단건 조회 - 숫자 조회**

```java
int rowCount = jdbcTemplate.queryForObject(
"select count(*) from t_actor", Integer.class);
```

하나의 로우를 조회할 때는 `queryForObject()` 를 사용하면 된다. 

지금처럼 조회 대상이 객체가 아니라 단순 데이터 하나라면 타입을 `Integer.class` , `String.class` 와 같이 지정해주면 된다.

**단건 조회 - 숫자 조회, 파라미터 바인딩**

```java
int countOfActorsNamedJoe = jdbcTemplate.queryForObject(
 "select count(*) from t_actor where first_name = ?", 
Integer.class, "Joe");
```

숫자 하나와 파라미터 바인딩 예시다.

**단건 조회 - 문자 조회**

```java
String lastName = jdbcTemplate.queryForObject(
 "select last_name from t_actor where id = ?",
 String.class, 1212L);
```

문자 하나와 파리미터 바인딩 예시이다.

**단건 조회 - 객체 조회**

```java
Actor actor = jdbcTemplate.queryForObject(
 "select first_name, last_name from t_actor where id = ?",
 (resultSet, rowNum) -> {
 Actor newActor = new Actor();
 newActor.setFirstName(resultSet.getString("first_name"));
 newActor.setLastName(resultSet.getString("last_name"));
 return newActor;
 },
 1212L);
```

객체 하나를 조회한다. 결과를 객체로 매핑해야 하므로 `RowMapper` 를 사용해야 한다. 여기서를 람다를 사용했다.

**목록 조회 - 객체**

```java
List<Actor> actors = jdbcTemplate.query(
 "select first_name, last_name from t_actor",
 (resultSet, rowNum) -> {
 Actor actor = new Actor();
 actor.setFirstName(resultSet.getString("first_name"));
 actor.setLastName(resultSet.getString("last_name"));
 return actor;
 });
```

여러 로우를 조회할 떄는 `query()` 를 사용하면 된다. 결과를 리스트로 반환한다. 결과를 객체로 매핑해야 하므로 `RowMapper` 를 사용해야 한다.



### 변경 ( Insert, Update, Delete )

데이터를 변경할 때는 `jdbcTemplate.update()` 를 사용하면 된다. 참고로 `int` 값을 반환하는데, `SQL`실행 결과에 영향받은 로우 수를 반환한다.

등록

```java
jdbcTemplate.update(
"insert into t_actor (first_name, last_name) values (?, ?)",
"Leonor", "Watling");
```

수정

```java
jdbcTemplate.update(
"update t_actor set last_name = ? where id = ?",
"Banjo", 5276L);
```

삭제

```java
jdbcTemplate.update(
"delete from t_actor where id = ?",
Long.valueOf(actorId));
```

**기타 기능**

임의의 `SQL` 을 실행할 때는 `execute()` 를 사용하면 된다. 테이블을 생성하는 `DDL` 에 사용할 수 있다.

**DDL**

```java
jdbcTemplate.execute("create table mytable (id integer, name varchar(100))");
```

**스토어드 프로시저 호출**

```java
jdbcTemplate.update(
 "call SUPPORT.REFRESH_ACTORS_SUMMARY(?)",
 Long.valueOf(unionId));
```

### 정리

실무에서 가장 간단하고 실용적인 방법으로 `SQL` 을 쓰려면 `JdbcTemplate`을 사용하면 된다. `JPA` 와 같은 기술을 사용하면서 동시에 `SQL` 을 직접 작성해야 할 때가 있는데, 그 떄도 같이 쓰면 된다.

단점으로는, 동적 쿼리 문제를 해결하지 못한다는 점이다. 게다가 문자열을 더해서 만들어야 한다는 단점도 있다.

이런 동적 쿼리 문제를 쉽게 해결하면서 `SQL` 을 작성할 수 있게 해주는 기술이 `MyBatis` 이다.
