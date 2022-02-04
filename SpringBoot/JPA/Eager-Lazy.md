### 즉시로딩, 지연로딩

1) `즉시로딩` : 엔티티를 조회할 때 연관된 엔티티도 함께 조회한다

ex)  회원과 팀 엔티티가 연관관계라고 가정 했을 때, 

 `em.find(Member.calss, "member1");` 를 호출 할 때 회원 엔티티와 팀 엔티티를 함께 조회한다.

예시 코드

```jsx
@ManyToOne(fetch = FetchType.EAGER)
```

회원을 조회하는 순간 연관관계를 맺은 엔티티 또한 함께 조회한다.

이때 2번의 쿼리를 실행하는 것 같지만, `JPA 구현체`는 즉시로딩을 최적화 하기위해서 

가능하면 조인 쿼리를 사용한다.

여기서는 한번의 쿼리로 두 엔티티를 모두 조회한다.

- 중요한 점

이떄 외부조인( outer join ) 을 사용하는 데 외부 조인을 사용할 경우 null을 허용하게 된다.

즉, 팀에 속하지 않는 회원까지 조회하게될 가능성이 있다.

하지만 외부조인 보다 내부조인이 성능 최적화에 유리한데 `NOT NULL` 조건을 사용하게 되면

내부조인만 사용해도 무방해지며 `JPA`에도 이러한 사실을 알려줘야 한다. 

그러면 알아서 내부조인을 사용하여 조회한다.

```jsx
@ManyToOne(fetch = FetchType.EAGER)
@JoinColumn(name = "TEAM_ID", nullable = false)
private Team team;
```

그리고 `@ManyToOne` 의 `optional` 속성으로 설정해도 내부 조인을 사용한다.

```jsx
@ManyToOne(fetch = FetchType.EAGER, optional = false)
```

`JPA` 는 선택적 관계면 외부조인을 사용하고, 필수 관계면 내부조인을 사용한다.

선택적관계 → `NULL` 허용

필수 관계 → `NULL` 미허용

2) `지연로딩` : 연관된 엔티티를 실제 사용할때 조회한다.

ex ) `member.getTeam().getName()` 처럼 조회한 팀 엔티티를 실제 사용하는 시점에 

`JPA`가 `SQL`을 호출해서 팀 엔티티를 조회한다.

예시코드

```jsx
@ManyToOne(fetch = FetchType.LAZY)
```

지연로딩의 경우 연관된 엔티티를 조회할 때 그 데이터를 사용하기전 까지 로딩을 미룬다.

```jsx
Member member = em.find(Member.class, "member1"); // 1
Team team = member.getTeam(); // 2
team.getName(); // 3
```

회원을 호출하면 팀을 즉시 조회하지 않고 3번처럼 실제로 사용되기 전까지는 로딩을 하지 않는다.

