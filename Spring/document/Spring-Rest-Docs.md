# Spring Rest Docs

`Spring REST Docs` 는 `RESTful API` 서비스의 문서화를 도와주는 도구이다. 

`Spring REST Docs` 는 문서 작성 도구로 기본적으로 [Asciidoctor](https://asciidoctor.org/) 를 사용하며, 이것을 사용해 `HTML` 을 생성한다. 

필요한 경우 Markdown 을 사용하도록 변경할 수 있다.

`Spring REST Docs` 는 Spring MVC 의 [테스트 프레임 워크](https://docs.spring.io/spring-framework/docs/5.0.x/spring-framework-reference/testing.html#spring-mvc-test-framework), Spring WebFlux WebTestClient 또는 [Rest Assured 3](http://rest-assured.io/) 로 작성된 테스트 코드에서 생성된 `Snippet` 을 사용한다. 

테스트 기반 접근 방식은 서비스 문서의 정확성을 보장해준다.  `Snippet` 이 올바르지 않을 경우 테스트가 실패하기 때문이다.

`RESTful` 서비스를 문서화 하는 것은 해당 리소스를 설명하는 것이다. 

리소스 설명의 핵심은 HTTP 요청과 HTTP 응답의 세부정보이다. `Spring REST Docs` 를 사용하면 리소스와 HTTP 요청 및 응답을 사용하여 서비스 내부의 세부 정보로부터 문서를 보호할 수 있다. 

즉, 서비스 내부 코드에 추가 또는 수정과 같이 영향을 주지 않고도 별도의 문서화 작업을 진행할 수 있게 된다. 이는 서비스 구현보다 API 를 문서화 하는데 도움이 된다. 

또한 문서를 재 작업 하지 않고도 버전에 따라 변경할 수 있다.

많이 사용되는 `RESTful API` 문서도구로는 대표적으로 `Swagger` 가 있다. 

아래는 `Swagger` 와 `Spring REST Docs` 의 비교 내용이다.

### Spring Rest Docs

1. 코드의 추가 및 수정이 없다.
2. 테스트 코드 작성이 필요하며, 테스트 성공 시 문서가 생성된다.
3. 버전 변화에 유연하고 정확성이 높다.

### Swagger

1. 코드에 어노테이션등을 추가해야 한다.
2. 테스트 코드 없이 서비스 쪽 코드 및 어노테이션 추가로 문서를 생성할 수 있다.
3. 버전 변화에 맞춰 재 작성해야 하며 이를 하지 않을 때 정확성이 낮다.

```java
plugins {  
	// Gradle 7.0 이상 버전부터 사용 가능
	id "org.asciidoctor.jvm.convert" version "3.3.2"
}

configurations {
	asciidoctorExt 
}

dependencies {
	asciidoctorExt 'org.springframework.restdocs:spring-restdocs-asciidoctor:{project-version}' 

// MockMvc 대신에 WetTestClient 또는 Rest Assured 를 사용할 경우 
// 다음 의존성을 추가하여 사용하면 된다
//- `spring-restdocs-webtestclient`
//- `spring-restdocs-restassured`
	testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc:{project-version}' 
}

ext { 
	snippetsDir = file('build/generated-snippets')
}

test { 
	outputs.dir snippetsDir
}

asciidoctor { 
	inputs.dir snippetsDir 
	configurations 'asciidoctorExt' 
	dependsOn test
}

bootJar { // SpringBoot 1.5 이하일 경우 jar
    dependsOn asciidoctor
    from ("${asciidoctor.outputDir}/html5") { // 운영환경 배포시에는 asciidoctor 태스크를 빼고
        into 'static/docs' // 해당 경로로 복사
    }
}

// 문서 수정 후 반영안될 경우 제거 후 생성되게 한다.
asciidoctor.doFirst {
    delete file('src/main/resources/static/docs')
}

task copyDocument(type: Copy) {
    dependsOn asciidoctor

    from file("build/docs/asciidoc")
    into file("src/main/resources/static/docs")
}

build {
    dependsOn copyDocument
}
```

추가 설정으로 `src/docs/asciidoc` 경로로 `*.adoc` 파일을 생성해둬야 한다. 

이후 `./gradlew build` 를 하면 `build/docs/asciidoc/*.html` 경로로 `*.adoc` 파일이 변환된다.

**( 해당 경로(`src/docs/asciidoc`)로 디렉토리를 정확하게 만들지 않으면  `build/docs/asciidoc/*.html` 가 생성되지 않는다. )**

추가 과정에서 `copyDocument` 에 의해 `build/docs/asciidoc` 경로의 `*.html` 파일을

`src/main/resources/static/docs` 경로로 복사할 수 있게 된다.

추후 문서 변경이후에 반영되지 않는 점을 고려해서 `asciidoctor.doFirst` Task를 선언한다.

공식문서 : [https://docs.spring.io/spring-restdocs/docs/current/reference/html5/#getting-started-build-configuration](https://docs.spring.io/spring-restdocs/docs/current/reference/html5/#getting-started-build-configuration)
