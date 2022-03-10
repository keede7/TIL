### DispatchServelt

![DispatchServlet](https://github.com/awse2050/TIL/blob/main/Spring/img/%EB%94%94%EC%8A%A4%ED%8C%A8%EC%B2%98%EC%84%9C%EB%B8%94%EB%A6%BF.PNG)


**동작 과정**

1. `핸들러 조회` : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.

2. `핸들러 어댑터 조회` : 핸들러를 실행 할 수 있는 핸들러 어댑터를 조회한다.

3. `핸들러 어댑터 실행` : 핸들러 어댑터를 실행한다.

4. `핸들러 실행` : 핸들러 어댑터가 실제 핸들러를 실행한다.

5. `ModelAndView` : 핸들러 어댑터는 핸들러가 반환하는 정보를 `ModelAndView`로 반환

6. `viewResolver 호출` : 뷰 리졸버를 찾고 실행한다.

7. `View` 반환 : 뷰 리졸버는 뷰의 논리 이름을 물리이름으로 바꾸고 렌더링 역할을 담당하는 뷰 객체를 반환한다.

8. `뷰 렌더링` : 뷰를 통해서 뷰를 렌더링 한다.
