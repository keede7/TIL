### @DataJpaTest 

`@DataJpaTest` 는 `@SpringBootTest` 와 다르게 순수하게 `Spring Data JPA` 자체만을 테스트 할 수 있게 도와준다.

스프링의 빈 주입과 더불어 전체적인 `Bean LifeCycle` 이 돌아가는 `@SpringBootTest` 보다 조금 더 빠르게 테스트를 할 수 있다.


#### @DataJpaTest Code

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@BootstrapWith(DataJpaTestContextBootstrapper.class)
@ExtendWith(SpringExtension.class)
@OverrideAutoConfiguration(enabled = false)
@TypeExcludeFilters(DataJpaTypeExcludeFilter.class)
@Transactional
@AutoConfigureCache
@AutoConfigureDataJpa
@AutoConfigureTestDatabase
@AutoConfigureTestEntityManager
@ImportAutoConfiguration
public @interface DataJpaTest { ... }
```

`@DataJpaTest` 는 `@Transactional` 을 같이 가지고 있기 때문에, 테스트를 할 때 `Select` 이외의 다른 `Query` 는 수행되지 않으므로 

로그에 나타나지 않지만, `@Rollback(false)` 또는 `@Commit` 을 선언하여 이외의 `Query` 가 로그에 찍히게 할 수 있다.


### 사용시 주의점

`Spring Data JPA` 를 사용할 떄, `PK` 의 생성 전략을 `IDENTITY` 를 사용하면 해당 생성전략 자체는 `영속성 컨텍스트`에 `Entity` 를 추가해야 하는데 `Id` 값이 필요하기 떄문에 `채번` 하기 위해 `INSERT` 를 수행하게 된다. 

하지만, 다른 생성전략은 `INSERT` 를 하지 않고 `PK` 값을 직접 지정한 후 `repository.save(..)` 를 하기 떄문에 테스트 메서드 전체에 `@Transactional` 이 붙어서 해당 메서드가 종료될 시점인 `트랜잭션의 커밋 시점` 에 `INSERT` 를 하기 떄문에, 원하는 결과가 안나올 수 도 있다.

#### 예시

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@IdClass(TestId.class)
@ToString
public class TestEntity  {

    @Id
    private Long groupCode;

    @Id
    private Long code;

    @CreationTimestamp
    @Column(nullable = false, updatable = false)
    private LocalDateTime regDate;

    @UpdateTimestamp
    @Column(insertable = false)
    private LocalDateTime modDate;

    public TestEntity(Long groupCode, Long code) {
        this.groupCode = groupCode;
        this.code = code;
    }
}
```

```java
@DataJpaTest
class TestEnttiyRepositoryTest {

    @Autowired
    private TestEnttiyRepository testEnttiyRepository;

    @Commit
    @Test
    void test() {
        TestEntity testEntity = new TestEntity(1L, 1L);

        TestEntity save = testEnttiyRepository.save(testEntity);
        System.out.println("save = " + save);
    }

}
```

* 결과
```java
Hibernate: 
    select
        testentity0_.code as code1_5_0_,
        testentity0_.group_code as group_co2_5_0_,
        testentity0_.mod_date as mod_date3_5_0_,
        testentity0_.reg_date as reg_date4_5_0_ 
    from
        test_entity testentity0_ 
    where
        testentity0_.code=? 
        and testentity0_.group_code=?
save = TestEntity(groupCode=1, code=1, regDate=null, modDate=null)
Hibernate: 
    insert 
    into
        test_entity
        (reg_date, code, group_code) 
    values
        (?, ?, ?)
```

결과적으로는 `@CreationTimestamp` 로 날짜를 자동 주입이 되지 않으며, 특정 비즈니즈 메서드 안에서 `save(..)` 를 통해 반환한 

`Entity`의 날짜를 이용하게 될 경우가 발생하면 이러한 상황이 발생해서 조금 곤란해질 수 있다.







