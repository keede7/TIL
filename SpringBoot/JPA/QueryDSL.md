

### QueryDSL

`SQL` , `JPQL` 을 **코드**로 작성할 수 있도록 도와주는 API이다,.

`Q도메인` 을 이라는 객체들을 사용해서 코드를 작성하게 된다.

### SQL, JPQL 문제점

- **문자열** 로 구성되어 있기 때문에, `Type-Check` 가 불가능하다.
- 해당 로직 실행 전까지는 동작여부를 알수 가 없다.

### QueryDSL 의 장점

- 문자가 아닌 코드로 작성한다.
- 코드로 작성하기 떄문에 컴파일 시점에 오류가 발생한다.
- 컴파일 시점에 오류를 잡기 때문에 실행시 예외에 대한 문제를 사전방지할수 있다.
- 단순한 모양이며 `JPQL` 과 코드 모양이 비슷하다
- 동적 쿼리

### QueryDSL - 동적 쿼리

- `JPQL` 의 경우 문자열을 더해야 한다.
- `QueryDsl` 은 코드를 더하는 것이기 때문에, 수월하게 가능하다.
- `BooleanBuilder` 에 조건을 넣고 쿼리를 추가로 발생시킬 수 있으며, 조건의 유무와 관계없이 실행시킬 수 있어 `동적 쿼리` 가 생성이 된다.
- `Entity` 가 아닌 `DTO` 타입으로 뽑아내는 기능또한 지원한다. ( ex) `@QueryProjection` )


### 설정 방법 ( Gradle 5.0 이상 )
```java
buildscript {
    ext {
        queryDslVersion = "5.0.0"
    }
}

plugins {
	// 추가
    id 'com.ewerk.gradle.plugins.querydsl' version '1.0.10'
}

dependencies {
	// 추가
    implementation "com.querydsl:querydsl-jpa:${queryDslVersion}"
    // Test Code에서 적용하기 위한 의존성
    implementation "com.querydsl:querydsl-apt:${queryDslVersion}"

}

// 바깥 하단에 추가

def querydslDir = "$buildDir/generated/querydsl"

querydsl {
    jpa = true
    querydslSourcesDir = querydslDir
}

sourceSets {
    main.java.srcDir querydslDir
}

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
    querydsl.extendsFrom compileClasspath
}

compileQuerydsl {
    options.annotationProcessorPath = configurations.querydsl
}
```
