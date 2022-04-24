### Chapter 1 Syntax

**1. 함수**

Java 와 다르게 다음과 같이 쓴다

반환타입이 `void` 일 경우 `Unit` 을 쓴다.

사실은 `Unit` 을 쓰지 않아도 무관하다.

```java
// fun helloWorld() { }
fun helloWorld() : Unit {
	println("Hello world");
}

```

**1-1 매개변수**

매개변수를 사용할 떄 타입을 **대문자**로 쓴다.

대신 반환값이 있을 경우 반환타입을 작성해줘야 한다.

```java
fun add(a: Int, b: Int) : Int {
	return a+b;
}
```

- 변수명을 타입보다 먼저 쓴다.
- 매개변수를 작성하고 타입을 지정한다.

**2. var , var**
- val : `value` 이며 바뀌지 않는 값이다.
- var : `variable` 이며 변할 수 있는 값을 의미한다.

값을 선언 할 때, 타입을 지정하지 않고 써도되지만,

val, var은 지정을 해줘야 함.

```java
var a : Int = 100
val b : Int = 9

val c = 100
var d = 100 // 자동추론

var name = "Joyce"
```

만약 변수를 값을 넣지 않고 지정할 경우에는 타입까지는 선언해줘야 한다.

```java
var e : String 
```

**3. String 템플릿.**

보통 문자열을 합칠 떄 + 를 많이 사용하는데,

`Kotlin` 에서는 `$` 를 사용해서 같이 쓸수 있다.

```java
val name = "hello";
println("my name is $name");
```

여러개를 합쳐 쓸떄는 `${...}` 형식으로 쓰면된다.

**4. 조건문**

Java의 조건문과 동일하다.

다른 것은 삼항연산자 뿐인데

다음과 같다. 이 떄, 타입 추론이 가능하여 반환타입을 안써도 된다.

```java
return a > b ? a : b; // Java
return if (a > b) a else b; // Kotlin
```

**5. When**

다른 언어에서 `Switch` 역할을 한다.

```java
when(score){
	0 -> println("dsdsds");
	1 -> println("sds");
	2,3 -> println("23");
	else -> println("sds");
}
```

복수로 써도 되고 이도저도 아니라면 `else`로 빠진다.

else를 안써줘도 되긴하지만 다음과 같이 써줄수도 있다.

```java
var b = when(score) {
	1 -> 1
	2 -> 2
	else -> 3 // 반환시 필수.
}
```

`when` 은 `Switch` 문과 같으며 `else` 를 써도 되지만.

만약 변수값에 반환해서 할당할 경우에는 `else`가 필요하다

```java
when(score) {
	in 90..100 -> prinln("90~100"); 
	in 10..80 -> println("10~80");
	else -> println("okay");
}
```

 **6.  Expression vs Statement 의 차이점.**

- `Expression`

어떤 것을 해서 값을 만들면 **표현식**이라고 한다.

예를 들어 `when` 의 경우 출력문으로 사용하거나 할당식으로 사용할 수 있는데,  우선 `Kotlin` 의 모든 함수를 **Expression**으로 사용이 된다.

**할당식** : **Expression**

**출력문** : **Statement**

함수의 경우에 반환타입이 있거나 `Unit`을 리턴하기 떄문에.

모든 함수를 **Expression**으로 사용된다.

**Java**에서 **void** 처럼 반환값이 없는 경우를 **Statement**라고 한다.


**7. Array, List**

- Array : 정해져있는 크기가 있다.
- List : 크게 2가지로 나뉜다,.
    - MutableList : 수정이 가능한
    - ImmutableList : 수정이 불가능한
    

```java
val array = arrayOf(1,2,3);
val list = listOf(1,2,3);
// 기본선언
val arrayList = arrayListOf<Int>();
```

Array의 경우 기본적으로 값을 변경할 수 있다.

List의 경우 Array와 다르게 인터페이스 이기 떄문에 읽기만 가능하다.

변경가능한 컬렉션을 쓸 경우 `arrayListOf` 를 써야한다.
