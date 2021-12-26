
## Thymeleaf의 특징

+ SSR
+ Natural Template
+ 스프링 통합 지원


#### 1. SRR
타임 리프는 백엔드 서버에서 HTML을 동적으로 렌더링하는 용도로 사용한다.

#### 2. Natural Template
순수 HTML을 최대한 유지하는 특징이 있다.
타임리프로 작성한 파일은 HTML을 유지하기 떄문에 브라우저를 통해 파일을 열어도 직접 내용을 확인할 수 있고, 
서버를 통해서 뷰 템플릿을 거치면 동적으로 변경된 결과를 확인 할 수 있다.


#### 3. 스프링 통합 지원
자연스럽게 스프링과 통합되고, 스프링의 다양한 기능 (EL) 을 편리하게 사용할수 있도록 지원한다.


##### 타임리프 선언 태그
HTML 파일의 상단의 `<html>` 태그를 수정한다.
```
<html xmlns:th="http://www.thymeleaf.org">
```


##### 대표적인 타임리프 기본 표현식
```

간단한 표현:
◦ 변수 표현식: ${...}
◦ 선택 변수 표현식: *{...}
◦ 메시지 표현식: #{...}
◦ 링크 URL 표현식: @{...}
◦ 조각 표현식: ~{...}

• 리터럴
◦ 텍스트: 'one text', 'Another one!',…
◦ 숫자: 0, 34, 3.0, 12.3,…
◦ 불린: true, false
◦ 널: null
◦ 리터럴 토큰: one, sometext, main,…

• 문자 연산:
◦ 문자 합치기: +
◦ 리터럴 대체: |The name is ${name}|

• 산술 연산:
◦ Binary operators: +, -, *, /, %
◦ Minus sign (unary operator): -

• 불린 연산:
◦ Binary operators: and, or
◦ Boolean negation (unary operator): !, not

• 비교와 동등:
◦ 비교: >, <, >=, <= (gt, lt, ge, le)
◦ 동등 연산: ==, != (eq, ne)

• 조건 연산:
◦ If-then: (if) ? (then)
◦ If-then-else: (if) ? (then) : (else)
◦ Default: (value) ?: (defaultvalue)

• 특별한 토큰:
◦ No-Operation: _

```
