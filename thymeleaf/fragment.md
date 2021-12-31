웹 페이지 개발 시 공통영역이 있다.

이러한 공통영역을 매번 사용하기엔 비효율적이므로 타임리프에서는

이러한 문제를 해결하기 위해서 템플릿조각 레이아웃 기능을 지원한다.

부분 포함 insert

```html
<div th:insert="~{template/fragment/footer :: copy}"></div>
```

```html
<h2>부분 포함 insert</h2>
<div>
	<footer>
	푸터 자리 입니다.
	</footer>
</div>
```

insert 사용 시 현재 태그(div) 내부에 추가한다.

replace

```html
<div th:replace="~{template/fragment/footer :: copy}"></div>
```

```html
<h2>부분 포함 replace</h2>
<footer>
푸터 자리 입니다.
</footer>
```

현재 코드를 대체한다.

~{} 를 사용하는 것이 원칙이지만 템플릿조각을 쓰는 코드가 단순하면 생략가능하다.

파라미터 사용

다음과 같이 파라미터를 전달해서 동적으로 조각을 렌더링할 수 있다.

```html
<div th:replace="~{template/fragment/footer :: copyParam ('데이터1', '데이터2')}"></div>
```
