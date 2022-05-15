### GraphQL

`SQL` 과 마찬가지로 쿼리 언어이다.

하지만, `SQL` 과 `GQL` 은 언어적 구조 차이가 크다. 또한 실전에서 쓰이는 방식도 다르다.

- `SQL` : DB시스템에 저장된 데이터를 효율적으로 가져오는 것이 목적
- `GQL` : `Client` 가 데이터를 서버로 부터 효율적으로 가져오는 것을 목적

`SQL` 은 주로 백엔드 시스템에서 작성하고 호출하는 반면에,

`GQL` 은 주로 `Client` 시스템에서 작성하고 호출한다.

### REST vs GraphQL

1. **통신 방식의 차이**

`REST` 의 경우 `Resources` 를 대상으로 통신을 하는 방식이라면,

`GraphQL` 의 경우 `Query` 를 사용해서 통신을 하는 방식이다.

`REST` 는 통신을 할 때 간편하게 호출이 가능하지만, 반환 받는 데이터가 복잡하다.

`GraphQL` 은 통신을 할 때 전달하는 `Query` 가 복잡할 수 있으나, 반환 받는 데이터에 대해서 좀 더 간단하게 받을 수 있고, `Client` 가 원하는 `Data` 만을 조정해서 가져다가 쓸 수 있다.

2. **EndPoint**

`REST` 의 경우 `GET` , `POST` , `DELETE` 등의 다양한 `Method` 와 함께 `URL` 을 조합하기 떄문에 다양한 `EndPoint` 가 존재한다. 하지만 `GraphQL` 의 경우에는 `POST` 만을 사용한다.

그래서 `REST` 는 `EndPoint` 마다 `SQL` 쿼리가 달리지지만, 

`GQL` 은 어떤 `Schema` 타입을 쓰냐에 따라서 `SQL` 쿼리가 달라지게 된다.

3. 장단점

`REST` 의 단점을 보완하기 위해 만들어졌지만, 

만약 특정 DB의 전체 필드를 가져오려할 경우 `Schema` 필드에 모든 필드를 선언하여 가져와야하는 단점이 존재한다. `REST` 에서 처럼 특정 `EndPoint` 로 모든 데이터를 쉽게 가져오는 방법은 없다.

### GraphQL 구조

1. **Query, Mutation**

`GraphQL` 에서 이 둘의 차이를 두고 사용하고 있다.

- `Query` :  주로 조회를 하는 경우에 사용하는 타입 ( R )
- `Mutation` : 주로 데이터의 변경시 사용하는 타입 ( C , U , D)

2. **스키마 타입 & 필드**

```graphql
type Object {
	name: String!
	appearsIn: [Episode!]!
}
```

- 타입 : `Object`
- 필드 : `name`, `appearsIn`
- 스칼라 타입 : `String` , `Int` 등등
- 느낌표 : `Non-Nullable` 데이터임을 알림.
- 대괄호 :  배열을 의미한다.

3. **스칼라 타입 종류**
- Int : 부호가 있는 32비트 정수
- Float : 부호가 있는 부동소수점 값
- String : UTF-8 문자열
- Boolean : true/false
- ID : 고유 식별자 값으로 String과 같은 방법으로 직렬화 되지만 ID로 정의 한다는 의미는 사람이 읽으라고 만든 의도가 아님을 의미함
