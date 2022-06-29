## JUnit
자바에서 쓰이는 **테스트 프레임워크**

**Spring Boot 2.2** 버전 부터 `JUnit 5` 를 기본으로 사용한다. 그 이전 버전들은 `JUnit 4`.

이전버전에서도 마찬가지였지만, `Reflection` 을 사용하기 때문에 더이상 테스트클래스 및 메서드에 `public` 을 붙이지 않아도 된다.

만약 `SpringBoot` 를 쓰지 않는다면 다음 의존성을 추가해야 한다.
```
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.5.2</version>
    <scope>test</scope>
</dependency>
```

### 기본 애노테이션

- **BeforeAll** : 테스트 메서드들이 모두 실행되기 전에 한번 호출된다. 이때는 `static void` 로 써야 한다.
- **AfterAll** : 모든 테스트가 실행된 이후에 한번 호출된다.
- **BeforeEach** , **AfterEach** : 모든 테스트를 각각 한번 씩 실행할 떄 마다 혹은 그 이전에 한번씩 실행된다.
- **Disabled** : 선언된 메서드는 전체 테스트 메서드시 실행되지 않게된다.

### 테스트 이름 표기방법.

**@DisplayNameGeneration**

- Method와 Class 레퍼런스를 사용해서 테스트 이름을 표기하는 방법 설정.
- 기본 구현체로 ReplaceUnderscores 제공

**@DisplayName (권장되는 방법)**

- 어떤 테스트인지 테스트 이름을 보다 쉽게 표현할 수 있는 방법을 제공하는 애노테이션.
- `@DisplayNameGeneration` 보다 우선 순위가 높다.

참고링크

- [https://junit.org/junit5/docs/current/user-guide/#writing-tests-display-names](https://junit.org/junit5/docs/current/user-guide/#writing-tests-display-names)

### Assertion


- **assertAll** : 모든 테스트 구문을 하나로 묶을 수 있으며, 모든 테스트의 검증이 가능하다.  

중간에 실패할 경우 이후 테스트가 진행이 안되므로 `assertAll` 로 이후 테스트까지 모두 검증가능.

- **assertTimeout** : 특정 시간 이내에 끝나야한다는 것을 검증 하는 테스트

### 조건에 따른 테스트

- **assumeTrue(조건)** : 해당 조건이 맞으면 이후 테스트를 진행한다.
- **assumingThat(조건, 테스트)** : 조건이 맞으면 작성된 테스트를 진행한다.

**애노테이션으로도 만들 수 있다 → Enabled___ , Disabled____**

- **@EnableOnOS** : 운영체제가 어떤것인지에 따라 테스트
- **@EnableOnJre** : 특정 JRE 버전일 경우 실행.
- **@EnabledIfEnvironmentVariable** : 환경변수에 따라 테스트 실행.

### 태깅, 필터링

태깅을 통해서 테스트를 그룹화 할 수 있다.

```java
<profiles>
        <profile>
            <id>default</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <artifactId>maven-surefire-plugin</artifactId>
                        <configuration>
                            <groups>fast</groups>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
        </profile>
<!--      profile을 ci로 설정할 경우 tag가 slow로 붙은 테스트만을 빌드한다.   -->
        <profile>
            <id>ci</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <artifactId>maven-surefire-plugin</artifactId>
                        <configuration>
                            <groups>slow</groups>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
```

### 커스텀 태그

사실 메타 애노테이션을 사용해서 할 수 가 있다.

즉 커스텀한 애노테이션을 만들 때 테스트관련 애노테이션을 써서 적용시킬 수 있다는 것이다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Test
@Tag("fast")
public @interface FastTest {
}

@FastTest
@DisplayName("스터디 만들기 1")
void create1() {
    Study study = new Study(10);
    assertAll(
            () -> assertNotNull(study),
            () -> assertTrue(1 < 2)
    );
    System.out.println("스터디 만들기 1");
}
```

### 테스트 반복하기

`@RepeatedTest`

- 반복 횟수와 반복 테스트 이름을 설정할 수 있다.
    - {displayName}
    - {currentRepetition}
    - {totalRepetitions}
- RepetitionInfo 타입의 인자를 받을 수 있다.

```java
@DisplayName("스터디 만들기")
@RepeatedTest(value = 10, name = "{displayName}, {currentRepetition}/{totalRepetitions}")
void create1(RepetitionInfo repetitionInfo) {
    Study study = new Study(10);
    assertAll(
            () -> assertNotNull(study),
            () -> assertTrue(1 < 2)
    );
    System.out.println("횟수 : " + repetitionInfo.getCurrentRepetition() + "/" +repetitionInfo.getTotalRepetitions());
    System.out.println("스터디 만들기 1");
}
```

`@ParameterizedTest`  - JUnit4 에서는 서드파티 필요.

- 테스트에 여러 다른 매개변수를 대입해가며 반복 실행한다.
    - {displayName}
    - {index}
    - {arguments}
    - {0}, {1}, ...
    

**인자 값들의 소스**

- **@ValueSource**
- **@NullSource, @EmptySource, @NullAndEmptySource**
- **@EnumSource**
- **@MethodSource**
- **@CsvSource**
- **@CvsFileSource**
- **@ArgumentSource**

#### 인자 값 타입 변환

- 암묵적인 타입 변환
- 명시적인 타입 변환
    - `SimpleArgumentConverter` 상속 받은 구현체 제공
    - `@ConvertWith`
    

단일 매개변수를 받아서 직접 변환시킬 경우.

```java
@DisplayName("ValueSource 값 객체 변환 테스트")
@ParameterizedTest(name = "{index}, {displayName} message={0}")
@ValueSource(ints = {10,20,40})
void valueSourceToConvertObjectTests(Integer limit) {
  System.out.println("limit : " + limit);
  System.out.println("new Study " + new Study(limit));
}
```

여러 변수를 받아서 변환시킬 경우 ( `@CsvSource` 를 사용)

**여기서 `Converter` 클래스는 `public` 또는 `static` 만 가능**

```java
@DisplayName("ValueSource 값 객체 변환 테스트")
@ParameterizedTest(name = "{index}, {displayName} message={0}")
@CsvSource({"10", "20", "40"})
void valueSourceToConvertObjectTests2(@ConvertWith(StudyArgumentConverter.class) Study study) {
    System.out.println("study : " + study);
}

// 단일 매개변수를 받아서 변환할 떄 사용한다.
static class StudyArgumentConverter extends SimpleArgumentConverter {
	@Override
	protected Object convert(Object o, Class<?> aClass) throws ArgumentConversionException {
	    assertEquals(Study.class, aClass, "Study 클래스여야 합니다.");
	    return new Study(Integer.parseInt(o.toString()));
	}
}
```

#### 인자 값 조합

- `ArgumentsAccessor`
- 커스텀 `Accessor`
    - `ArgumentsAggregator` 인터페이스 구현
    - `@AggregateWith`

**값 타입 변환 및 값 조합은 `public` 또는 `static` 클래스로 만들어야 사용이 가능하다.**

```java
@DisplayName("CsvSource 값 객체 직접 변환 테스트")
@ParameterizedTest(name = "{index}, {displayName} message={0}")
@CsvSource({"10, 자바", "20, 스프링", "40, PHP"})
void csvSourceToAggregateObjectTest(Integer limit, String name) {
    System.out.println("limit : "+ limit + ", name : " + name);
    System.out.println("new Study ... : " + new Study(name, limit));
}

@DisplayName("CsvSource 값 객체 직접 변환 테스트")
@ParameterizedTest(name = "{index}, {displayName} message={0}")
@CsvSource({"10, 자바", "20, 스프링", "40, PHP"})
void csvSourceToAggregateObjectTest2(ArgumentsAccessor accessor) {
    System.out.println("new Study ... : " + new Study(accessor.getString(1), accessor.getInteger(0)));
}

@DisplayName("CsvSource 값 Aggregator를 통한 변환 테스트")
@ParameterizedTest(name = "{index}, {displayName} message={0}")
@CsvSource({"10, 자바", "20, 스프링", "40, PHP"})
void csvSourceToAggregateObjectTest2(@AggregateWith(StudyAggregator.class) Study study) {
    System.out.println("study : " + study);
}

static class StudyAggregator implements ArgumentsAggregator {
    @Override
    public Object aggregateArguments(ArgumentsAccessor argumentsAccessor, ParameterContext parameterContext) throws ArgumentsAggregationException {
        return new Study(argumentsAccessor.getString(1), argumentsAccessor.getInteger(0));
    }
}
```

참고

- [https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests)

### 테스트 인스턴스

매번 테스트를 할 때마다 다른 인스턴스 값을 만들어서 사용한다.

왜냐하면 테스트간의 의존성을 없애기 위해서 이다.

인스턴스를 하나만 쓰면 서로 공유해서 쓰기 떄문이다. 게다가 순서또한 랜덤이다.

JUnit5 에서는 기본 전략을 변경하는 방법이 추가가 되었다.

테스트 클래스 인스턴스를 테스트 메서드 마다 만들어쓰는게 아니라 하나로 공유해서 쓰는 방식이 생겼다.

그래서 조금이라도 성능적으로 장점이 있을 것이며, 

제약이 있던 부분이 조금 느슨해진다.

그리고 하나로 인스턴스를 공유하므로 `@BeforeAll` , `@AfterAll` 이 `static` 일 필요가 없어진다.

JUnit은 테스트 메소드 마다 테스트 인스턴스를 새로 만든다.

- 이것이 기본 전략.
- 테스트 메소드를 독립적으로 실행하여 예상치 못한 부작용을 방지하기 위함이다.
- 이 전략을 JUnit 5에서 변경할 수 있다.

`@TestInstance(Lifecycle.PER_CLASS)`

- 테스트 클래스당 인스턴스를 하나만 만들어 사용한다.
- 경우에 따라, 테스트 간에 공유하는 모든 상태를 `@BeforeEach` 또는 `@AfterEach`에서 초기화 할 필요가 있다.
- `@BeforeAll`과 `@AfterAll`을 인스턴스 메소드 또는 인터페이스에 정의한 default 메소드로 정의할 수도 있다

```java
// 테스트 메서드들이 해당 클래스의 한개의 인스턴스를 공유해서 사용한다.
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
public class TestInstanceTests {

    int value = 1;

    @BeforeAll
//    static void beforeAll()
    void beforeAll() {
        System.out.println("beforeAll.....");
    }

    @AfterAll
//  static void afterAll() {
    void afterAll() {
        System.out.println("afterAll.....");
    }

    @Test
    void addTest1() {
        System.out.println(value++);
    }
    
    @Test
    void addTest2() {
        System.out.println(value++);
    }
}
```

### 테스트 순서

실행할 테스트 메소드 특정한 순서에 의해 실행되지만 어떻게 그 순서를 정하는지는 의도적으로 분명히 하지 않는다. (**테스트 인스턴스**를 테스트 마다 새로 만드는 것과 같은 이유)

그리고 테스트 순서에 의존하면 안된다. 

애초에 제대로 작성된 단위테스트라면 각 테스트마다 독립적으로 기대하는 결과들이 나와야 하기 때문이다.

경우에 따라, 특정 순서대로 테스트를 실행하고 싶을 때도 있다. 그 경우에는 테스트 메소드를 원하는 순서에 따라 실행하도록 `@TestInstance(Lifecycle.PER_CLASS)`와 함께 `@TestMethodOrder`를 사용할 수 있다.

- **MethodOrderer** 구현체를 설정한다.
- 기본 구현체
    - **Alphanumeric**
    - **OrderAnnoation**
    - **Random**

무조건 같이 쓸 필요는 없지만 상태를 유지하면서 테스트할 경우에는 하나의 인스턴스를 같이 쓰면서 하면 된다.

### **junit-platform.properties**

JUnit 설정 파일로, 클래스패스 루트 (src/test/resources/)에 넣어두면 적용된다.

```java
테스트 인스턴스 라이프사이클 설정
junit.jupiter.testinstance.lifecycle.default = per_class

확장팩 자동 감지 기능
junit.jupiter.extensions.autodetection.enabled = true

@Disabled 무시하고 실행하기
junit.jupiter.conditions.deactivate = org.junit.*DisabledCondition테스트 이름 표기 전략 설정

junit.jupiter.displayname.generator.default = \
org.junit.jupiter.api.DisplayNameGenerator$ReplaceUnderscores
```

### 테스트 확장 모델

`JUnit 4` 에서는 `@RunWith(Runner)`, `TestRule`, `MethodRule`.

`JUnit 5` 에서는 `@ExtensionWith`

**확장팩 등록 방법**

- 선언적인 등록 `@ExtendWith`
    
    ```java
    // 선언적 확장
    @ExtendWith(FastSlowTestExtension.class)
    public class TestInstanceTests {
    }
    ```
    
- 프로그래밍 등록 `@RegisterExtension`
    
  ```java
  @RegisterExtension
  private FastSlowTestExtension fastSlowTestExtension = new FastSlowTestExtension(1000L);
  ```

  생성자 코드가 들어가기 때문에, 선언적으로 등록할 경우 예러가 발생한다.
    

- 자동 등록 자바 [ServiceLoader](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/ServiceLoader.html) 이용
