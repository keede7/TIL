
### 날짜 및 유틸 객체

타임리프는 문자, 숫자, 날짜, URI 등을 편리하게 다루는 다양한 유틸리티를 제공

#### 유틸 객체

`#message` : 메시지, 국제화 처리

`#uris` : URI 이스케이프 지원

`#dates` : java.util.Date 서식 지원

`#calendars` : java.util.Calendar 서식 지원

`#temporals` : 자바8 날짜 서식 지원

`#numbers` : 숫자 서식 지원

`#strings` : 문자 관련 편의 기능

`#objects` : 객체 관련 기능 제공

`#bools` : boolean 관련 기능 제공

`#arrays` : 배열 관련 기능 제공

`#lists` , `#sets` , `#maps` : 컬렉션 관련 기능 제공

`#ids` : 아이디 처리 관련 기능 제공


#### 자바 8 날짜 지원

타임리프에서 자바8날짜인 `LocalDate` , `LocalDateTime`, `Instant` 를  사욯하려면 추가적인 라이브러리가 필요한데, 

스프링 부트 타임리프를 사용하면 해당 라이브러리가 자동으로 추가된다.



------

##### 기본적인 사용예시

타임리프 문법에 `#{temporals.XXX}` 형식으로 갖추면 된다.

```
${#temporals.format(localDateTime, 'yyyy-MM-dd HH:mm:ss')}
```

기본적으로 `day`, `month`, `dayOfWeek`, `hour`, `minute`, `second` 등의 
여러가지 포맷형식을 지원한다.


