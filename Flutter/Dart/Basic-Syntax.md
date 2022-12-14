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


#### 2-3 `final`, `const`

`final` 은 `Java`에서 사용하는 것과 동일하게 `final` 로 선언한 변수는 값을 재할당 할 수 없다

```java
final String name = "하하";

name = "헤헤" // Error!!
```

`const` 또한 동일하게 재할당을 할 수 없다.


`final` 과 `const` 를 사용할 경우 `var` 키워드를 생략해서 사용할 수 있다.

```java
final name = "하이" // == final String name
const name2 = "하이" // == final String name2
```

- `final`과 `const` 의 차이점

1. `final` 은 빌드타임의 값을 알지 않아도 된다.
2. `const` 는 빌드타임의 값을 알고 있어야 한다.

> `빌드타임` 이란 `Run` 을 하면 빌드를 하는 순간을 `빌드타임` 이라고 한다.

예를 들어, `DateTime` 인 시간을 표현하는 값이 있는데, `DateTime.now()` 를 할당한 변수는 사용되는 시점에 값이 생성된다.

즉, 빌드를 하는 시점에는 해당 변수에 값이 할당이 되어있지 않은 상태이기 때문에 빌드타임에 알 수 없는 값인데,

`const` 로 선언을 할 경우 `const`는 빌드타임에 값을 알아야 하는 키워드이므로 붙여서 사용할 수 없고

`final` 로 사용해서 쓸 수 있다.

```
  final DateTime now = DateTime.now(); // OK
  const DateTime now = DateTime.now(); // Error
```


#### 3. Operator

`Java` 에서 사용되는 덧셈 ,뺄셈 등등의 연산을 말한다.

#### 3-1 Null Operator

숫자값을 선언 할 떄도 `?` 를 붙이면 해당 숫자타입의 변수에 `null` 을 선언할 수 있다.

`null` 이 가능한 값의 변수는 `??=` 를 사용해서 `null` 일 경우 다른 값을 할당시킬 수 있다.

```
double? d = 4.0;
d = null // OK

d ??= 3.0;
print(d) // 3.0
```
#### 3-2 Operator 타입 비교

`변수 is 타입` 을 사용해서 비교할 수 있다.

```
int number =1 ;
print( number is int ) // true
print( number is String ) // false
```


#### 4. List

여러 개의 값을 넣을 수 있는 컬렉션과 같은 역할을 한다.


#### 5. Map

`key` - `value` 로 구분되는 집합체이다.

```
Map<String, String> dic = {};

dic['Hulk'] = "헐크"; // 추가

dic.remove('Hulk') // 삭제

dic.keys // 키 값만 전체 조회

dic.values // Value 값만 전체 조회


```


#### 6. Set

`Java` 의 컬렉션과 동일하고 중복을 허용하지 않으며 메서드 또한 사용방법이 동일하다.


#### 7. 반복문 & 조건문

`Java` 문법과 일치한다. 추가로 `in` 키워드를 통해서 `향상된 for문` 을 사용할 수 있다.

```
List<int> nubmers = [1,2,3,4,5];

for (int number in numbers) {
  ....
}

```


#### 8. enum

사용방식은 `Java` 와 비슷하다.

```
enum Status {
  approved, 
  pending,
  rejected
}

Status status = Status.pending;

if( status == Status.pending) {
...
} else {
...
}

```

#### 9. Function 

- 타입을 생략해서 작성할 수 있으며 생략할 경우 `dynamic` 타입으로 선언된다.

```

addNumber(int x, int y, int z) {
  return x+y+z;
}

```

- 직접 기본값을 설정해서 사용할 수 있다.

```

addNumber(int x, [int y = 30, int z=20]) {
  return x+y+z;
}

```

- 메서드의 파라미터는 보통 순서대로 넣어야 하지만, 이름을 부여해서 순서에 상관없이 작성하게 할 수 있다.

- `required` 키워드가 붙은 값은 이름을 무조건 부여해서 선언해야하고, 붙이지 않는다면 이름을 선언하지 않아도 되지만 기본값을 다시 설정해야 한다.

```

addNumber({
  required int x,
  reuiqred int y,
  int z = 10
}) => x+y+z;

void main {
  addNumber ( y:20 , x:30);  // 60
}

```

- 화살표 함수를 사용해서 쓸수 있다.
- 
```
addNumber({
  required int x,
  reuiqred int y,
  int z = 10
}) => x+y+z;
```

#### 10. typedef

함수를 조금 더 함축적으로 사용할 수 있는 방법이다.

해당 키워드를 사용해서 변수에 함수를 할당시킨다.  그리고 그 함수의 `시그니처(선언부)` 를 일치시킨 메서드가 있으면

해당 타입으로 메서드를 사용할 수 있게 해준다.

```
typedef Operation = int Function(int x, int y, int z);

int add(int x, int y, int z) => x+y+z;
int sub(int x, int y, int z) => x-y-z;

// 이렇게 사용을 잘 하지는 않는다.
void main() {

  Operation oper = add;
  
  int result = oper(10,20,30);

}

// 주로 다음과 같이 사용한다.

int calc(int x, int y, int z, Operation oper) {
  return oper(x,y,z);
}

void main() {
  calc(10,20,30, sub); // -40
}

```



