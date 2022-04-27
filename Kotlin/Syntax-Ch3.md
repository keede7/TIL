- **람다식**

람다식은  우리가 마치 Value 처럼 다룰 수 있는 익명함수.

즉, 자바의 익명함수와 쓰임이 비슷해서 그렇게 불린다.

1) 메서드의 파라미터로 넘겨줄 수 있다.

2) 리턴값으로 사용할 수 있다.

```java
val lambdaName : Type = {args -> codeBody}
val squance : (Int) -> Int = {number -> number * number}
```

타입 추론이 가능한 것은 Int, Int를 작성했기 때문이다.

만약 작성하지 않으면 추론이 불가능하여 에러가 발생한다.

하지만, 다음과 같이 한 곳에라도 타입을 작성하면 상관없다.

매개변수를 받는 라인은 `()` 괄호를 붙여줘야 한다.

매개변수의 개수가 여러 개 일 수 있기 때문이다.

결과 타입 라인은 안써줘도 무방하다.

```java
var square : (Int) -> Int = { num -> num * num }
var square = {num : Int -> num * num}
```

- **람다의 표현방식**

람다를 표현하는 방법은 여러가지가 있다.

3-1 함수식을 만들지 않고 즉시 익명함수로 전달해서 사용 = 람다 리터럴

```java
fun sample(lambda : (Double) -> Boolean) : Boolean {
      return lambda(5.4)
}

val lambda2 = {num: Double -> num == 3.4}
var lambda3 : (Double) -> Boolean = { num -> num == 3.4 }

sample(lambda2)
sample(lambda3)
// 람다에 대한 조건을 직접 넣어서 사용한다.
sample({it > 3.22})
sample {it > 3.22}
```

중괄호를 직접 넣어서 사용도 가능하며, 대신 매개변수의 마지막위치에서 사용이 가능하다.  `it` 의 경우 매개변수가 1개일 떄 사용 가능하다.

- **Data Class**

말 그대로 `Data` 를 담는 그릇이 된다.

`Java` 에서의 클래스를 `POJO` 방식으로 만들때와 같다고 보면된다.

`toString` , `hashCode` 등등의 메서드가 구현된 클래스이다.

```java
// toString, hashCode, equals, copy, property, constructor
data class Ticket(val companyName: String,
                    val name: String,
                    var date: String, var seatNumber: Int)
```

- **Companion Object**

`Java` 의 `static` 대신 정적 메서드, 변수를 만들 때 사용한다.

`private constructor` 를 사용해서 다른 곳에서 객체를 생성하지 못하게 해두는 방식을 사용한다. 

또한, 상속이 가능하다.

```java
class Book private constructor(){

    companion object BookFactory : BookInterface {
        private val MAX_PAGE : Int = 245;

        override fun getMaxPage(): Int {
            return MAX_PAGE;
        }
    }
}

interface BookInterface {
    fun getMaxPage(): Int
}

fun main() {
    val maxPage = Book.BookFactory.getMaxPage();
    println("MAX PAGE : $maxPage")
}
```

`BookFactory` 처럼 이름을 설정해 줄 수 있고 안해도 된다.

대신 이름을 설정 했을 때와 안했을 때의 차이점이 존재한다.

+ 설정했을 경우 : `Book.이름` 으로 호출해야 한다.
+ 미설정일 경우 : `Book.Companion` 으로 호출이 가능하다.

`Companion` 예약어를 붙일수 있다-없다의 차이이다.

- **Object Class**

object 클래스는 다른 클래스와 다르게, Spring의 애플리케이션 구동시 

딱 한번만 객체가 생성되는 `싱글톤 패턴` 의 클래스이다.
