### Syntax Chapter 2

1. 반복문

`For` 와 `While` 을 사용한다.

`in` 이라는 연산자로 반복문을 사용한다

```java
val students  = arrayListOf("asas", "dgf", "dffd", "dre");

for (name in students) {
	println("${name}");	
}
// 1부터 10까지 돌린다.
for(i in 1..10) {
	println(i);
}
```

추가적으로 `step` , `downTo` , `until` 이 있다.

`step` 은 입력한 값 만큼 띄워서 출력한다.

`downTo` 는 동작 순서를 반대로한다. 대신 입력 값이 역순이 아닐 경우 실행되지 않는다.

```java
    // downTo 역순출력, 조건안맞을 경우 미출력
    // step 입력값 만큼 띄워 출력된다.
    for(num in 20 downTo 10 step 3) { // 20, 17 ,14, 11
        println(num);
    }
```

`until` 은 마지막범위의 값을 포함하지 않는다.

```java
    for(num in 1 until 10) { // 1,2,3,4,5,6,7,8,9
        println(num);
    }
```

`withIndex` 는 반복문을 사용할 때 `index` 값 또한 출력할 수 있게 해준다.

```java
for ( (index, name) for ( (index, name) in students.withIndex()) {
        println("index : $index , name : $name")
    }
```

2. `NonNull` , `Nullable` 

`Java` 와 `Kotlin` 간의 차이점 중 하나이다.

`NullpointException` 은 컴파일시점에서 잡을수 없으며 런타임시점에서만 잡을 수 있다.

그래서 `Kotlin` 에서는 컴파일 시점에 잡기 위해서 `?` 가 나왔다.

`var name : String = "jojo"` 의 경우 `Null` 이면 안되는 타입이다.

`var nullName : String = null` 을 쓰면 `Null` 이 안되는 타입으로 컴파일시점에 에러가 나와있다.

이것은 타입 옆에 `?` 를 추가하면 `Nullable` 변수가 된다.


```java
var name = "dfsdf"
var nullName = : String? = null;
var nameUpper  =  name.toUpperCase();
var nullNameUpper = nullName?.toUpperCase();
```

3. `Elvis 연산자`  ( `?:` )

만약 기본값을 주고 싶을 때 사용한다. 해당 변수가 `Null` 이라면 입력한 값을 기본값으로 갖는다.

```java
var name = "sdjskdn"
var lastName : String? = "Hong"
var fullName = name + " " + (lastName?: "No Last Name")
```

4. `!!` - 컴파일러에게 null이 아님을 보증한다고 설정하는 것을 말한다.

그렇기 떄문에 `?` 를 써주지 않아도 된다. 대신 사용을 거의 지양하는데,

확실하게 null이 아니지 않은 이상은 쓰지말고 `Elvis 연산자`를 쓰는게 낫다.

5. `let 연산자`

특정 값이 `null` 이 아니라면 다음 로직을 실행시키는 것이다.

`null` 이라면 입력한 익명함수를 실행시키지 않는다.

스스로 객체를 인자로 받아 사용하며 `it` 키워드로 호출할 수 있다.

또는 익명함수 호출과 같은 표현식을 쓸 수 있다.

```java
// ?를 사용하면 null이 아닐떄 실행되고 안붙이면 항상실행.
str?.let { println( "$it dsf") }
str?.let { i -> println("$i ...") }
```

6. `Class` 

`Kotlin`은 `Java`와 다르게 파일이름과 클래스이름이 일치하지 않아도 되고

여러 클래스를 한 파일에 넣을 수 있다.

자바에서는 `new` 키워드를 썻지만, 코틀린은 함수호출식으로 써주면 된다

```java
class Human { 
	fun cake() {
	}
}

fun main() {
	val human = Human();
	human.cake();
}
```

만약 객체 생성시 변수를 지정하고 싶을 떈 생성자를 만들면된다.

`Java` 와 조금 다른점이 있다.

`Kotlin`은 클래스명 옆에 `constructor` 를 써줘야 한다. 생략도 가능하다. 대신 `Java`처럼 기본생성자가 없어진다.

```java
class Human constructor(name: String) {
	val name : String = name
}

```

만약 클래스의 `property` 명칭과 생성자에 변수명칭이 같으면

다음과 같이 쓸 수 있다.

```java
class Human (val name: String) {
	
}
```

기본값을 줄 수 도 있다. 기본값을 줄 경우 매개변수를 받는 생성자 뿐만 아니라 기본생성자로 생성지 기본값을 넣어서 만들어 준다.

```java
class Human(val name: String = "default Name..") {
}
```

6-1 `init`

 `init` 을 쓸 경우 인스턴스 생성시 호출할 로직을 작성할 수 있다.

주 생성자의 로직이기 떄문에 가장 먼저 호출된다.

```java

class Human(val name: String = "default Name..") {

    init {
        println("init !!!")
    }
}
```

부 생성자도 만들어 줄 수 있다. - 즉 매개변수가 다른 생성자를 만들수있다.

이때는 내부에 `constructor` 를 작성해준다.

```java
    constructor(name: String, number: Int) : this(name) {
        println("name : $name , number : $number")
    }
```


이때 부 생성자는 주 생성자를 위임을 받아야 한다 `this` 

만약 주 생성자가 없다면 `this` 를 쓰지않아도 된다.

그리고 IDE가 주 생성자로 쓰게끔 추천한다.


9-1 `상속`

```java
class Child : Parent() {

}
```

하지만 위 처럼 사용할 경우 에러가 발생하는데

코틀린은 기본적으로 `final` 클래스이기 떄문에 상속을 해줄수 없다,.

그래서 `open` 이라는 예약어를 통해서 상속을 할 수 있게된다.

```java

open class Parent {
    open fun printss() {
        println("sksksksk");
    }
}

class Child : Parent() {
    override fun printss() {
        println("qwqwqwqwq");
    }
}

```

9-2 오버라이딩

`override` 예약어를 써야한다. 그런데 상속때와 마찬가지로 메서드 또한 final이 되어서 부모 메서드에 `open` 예약어를 작성해둬야 한다.

부모 메서드를 사용하려면 자바와 마찬가지로 `super` 를 써야 한다
