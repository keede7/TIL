## Dart 기본기 문법

### 1. 변수의 선언방법

> 기본적으로 `Java` , `Kotlin` 과 유사하며 특히 `null` 처리방식에 따라 `Kotlin` 에 더 가깝다.

```dart

var 변수명 = 'test'
변수명 = 'hello'

```

### 2. 변수의 타입

| Type | 명칭 |
|---|----|
| `int` | 정수 |
|`double` | 실수 |
|`bool` | 참,거짓|
|`String` | 문자 |
| `var` | 모든 타입|
|`dynamic` | 모든 타입|

#### 2-1 `var` 과 `String` 의 차이점.

`var` 는 기본적으로 입력한 값의 타입에 추론하여 결정된다.

`String` 은 오로지 `문자열` 형태로 저장이된다.

이 떄 `runtimeType` 을 사용해서 해당 변수의 타입을 확인할 수 있다.

```dart
var name = '블랙';

print(name.runtimeType) // String

```

#### 2-2 `var` 과 `dynamic` 의 차이점

둘 다 모든 타입의 값이 들어갈 수 있지만 `var` 는 최초로 들어간 값의 타입으로 재선언 할 수 있으며

`dynamic` 의 경우에는 최초 선언 값과 무관하게 다른 타입의 값으로 재선언이 가능하다.

```dart

var name = 'Hello';

name = 123;  // Error!!

dynamic age = 123;

age = "hello"; // OK

```

#### 2-3 문자열과 `Nullable`

문자열은 다음과 같이 출력할 수 있다.

문자 하나만 쓸 경우 `{}` 를 생략할 수 있으나 함수를 사용할 경우 `{}` 가 필수이다.

```dart
String name = "홍길동";

print(name) // 홍길동
print('$name');
print('${name}');

```

이미 값이 선언된 `String` 타입의 변수는 `null` 로 재선언하면 에러가 나오는데,

선언시 `?` 를 붙여서 `null` 로 선언시키게 할 수 있다.

```dart
  String name = "헬로";
  
  name = null // Error!!!
  
  String? name2 = "하이";
  
  name2 = null // OK!!
  
```

만약 어떤 함수를 실행할 떄 절대 `null` 이 안되는 변수라면 `!` 를 붙여서 다음과 같이 선언한다.

```dart
print( name2! );
```




