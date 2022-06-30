## Mockito

테스트, 특히 **단위 테스트(Unit Test)**를 할 때 가장 많이 사용되는 `Mock` 프레임워크.

주로 Database, 외부 API 등이 어떻게 동작하는지를 직접 예측해서 **가짜 객체**를 만들어두고

그 객체들을 가지고 `Mock` 프레임워크를 이용해서 코딩하고 예측과 일치하는 행위를 한다고 가정하에 테스트를 하는 것.

### 설정방법

`SpringBoot 2.2` 이상부터는 프로젝트 생성시에 `spring-boot-starter-test` 의존성에 의해 자동으로 `Mockito` 가 추가된다.

`SpringBoot` 를 쓰지 않으면 아래의 의존성이 필요하다.

```java
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>3.1.0</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>3.1.0</version>
    <scope>test</scope>
</dependency>
```

### Mock을 사용할 때 알아둘 점

- `Mock` 을 만드는 방법
- `Mock` 이 어떻게 동작해야 하는지 관리하는 방법
- `Mock` 의 행동을 검증하는 방법.


### Mock 객체 만드는 방법

1) `Mockito.mock(..)` 을 활용하는 방법
```java
  MemberService service = mock(MemberService.class)
  StudyRepository repository = mock(StudyRepository.class)
```

2) `@Mock` Annotation을 활용하는 방법
이 때, `MockitoExtension` 을 명시해야 한다.

```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {
    @Mock MemberService memberService;
    @Mock StudyRepository studyRepository;
}
```

### Mockito 메서드 구조

- `Mockito.when(...)`
흔히 말하는 `Given`, `When`, `Then` 구조에서 `Given` 에 속하는 메서드이다.

명시적으로는 `When` 의 역할일 것 같지만 `Mockito` 에서는 해당 메서드로 작성하여 쓴다.

```java
  Member member = new Member();
  member.setId(1L);
  member.setEmail("wesaq@naver.com");

  Study study = new Study("하이", 10);
  when(memberService.findById(anyLong())).thenReturn(Optional.of(member));
  when(studyRepository.save(study)).thenReturn(study);
```


- `Mockito.verify(...)`
`Then` 구조에 속한다. 검증을 하는 단계이다.

```java
  verify(memberService, times(1)).notify(member); 
```


### Stubbing
`Mock` 객체의 행동을 조작하는 것이다.

즉 어떤 행동에 대해서 특정한 객체로 반환받고 싶다고 가정하는 것이다.


### Mockito BDD 스타일 API
**행위 주도 개발**이라고 하며, `TDD` 에서 창안됐다.

`Mockito` 패키지 보다 메서드의 이름이 조금 더 명확해져서 **코드의 가독성**이 높아진다.

```java
  Member member = new Member();
  member.setId(1L);
  member.setEmail("wesaq@naver.com");

  Study study = new Study("하이", 10);
  // BDDMockito 패키지의 메서드
  // ==  when(memberService.findById(anyLong())).thenReturn(Optional.of(member));
  given(memberService.findById(anyLong())).willReturn(Optional.of(member));
  given(studyRepository.save(study)).willReturn(study);
  
  studyService.createNewStudy(2L, study);

  // == verify(memberService, times(1)).notify(member);
  then(memberService).should(times(1)).notify(study);
  then(memberService).should().notify(member);
  then(memberService).shouldHaveNoMoreInteractions();
```

---

#### 출처
[더 자바, 애플리케이션을 테스트하는 다양한 방법](https://www.inflearn.com/course/the-java-application-test/dashboard)

