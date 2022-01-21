### Basic Syntex



#### 기본적인 PHP 코드 형식

```html
<html>
<body>

<?php
// 항상 php태그 안에서 php 코드가 동작하게 된다.
// 주석의경우 #를  써도되고 //를 써도 상관없다.

// php태그의 닫는 코드는 굳이 작성하지 않아도 에러는 나지 않는다.
// 하지만 작성을 하는 것이 더 좋아보임.
?>

</body>
</html>
```

1.  PHP 변수의 경우 `$변수명` 을 통해서 작성한다

```php
$a = 1; 
```

2. 문자열을 표현할 경우에는 `""` 또는 `''` 을 사용한다.

```php
$title = "title";
$title2 = 'title';
```

3. 문자열과 문자열을 합치고 싶은 경우  `.` 을 사용한다.

```php
$title = "title";
$composer = $title.1;
```

4. 특정 변수의 타입을 확인하고 싶을 때는 var_dump를 사용한다

```php
 $a = "stirng";
 var_dump($a);
```

5. 특정한 변수의 타입을 알고싶을 떄는 gettype()을 사용해도된다

var_dump와 gettype의 차이점은 var_dump는 출력과 검사를 동시에 하지만, 

gettype은 검사만 한다. 

```php
$a = 1;
var_dump($a); // 검사와 출력을 동시에 해준다
echo gettype($a); // 출력을 하기 위해서 echo를 사용해줌.
```

6. 특정한 값의 타입을 변경하고자 할때는 settype을 써준다

```php
$a = 100;
settype($a, 'double');

```

정수형과 실수형데이터를 계산 할떄는 크게 문제는 없을 수 있겠지만

계산을 할때 데이터의 타입을 맞춰주고 하는것이 좀 더 좋다.

7. 상수를 선언할때는 `define` 을 사용해야 한다,.

```php
define("TITLE", "ex");
echo $TITLE;
```

8. 조건문의 경우 java와 동일하게 이루어진다. 하지만 Form데이터를 받아서

페이지를 이동했을 떄 전달받은 데이터를 가지고 조건문을 실행할 수 있다.

```php
if($_get['id'] === "???") {
	echo 
}
```

POST 로 왔을 경우에는 `$_POST` 를 사용해야 한다.

조건문 비교는 `AND` , `OR` , `&&` , `||` 모두 지원한다.

9. PHP는 JS와 마찬가지로 true, false에 대한 여러가지 변환데이터들이 있다.

```php
ex ) 0, null, "" = false , 1 = true
```

10. 반복문 또한  java와 동일하다

```php
for ( $i = 0; $i < 10; $i++) {
	echo "countie" . i . "GG"
// echo "countine{$i}GG";
}
```

11. 함수의 경우 java와 동일하지만 기본값을 설정하는 방법이 존재한다.

```php
function test( $arg = 100 ) {
	return $arg;
}

echo test(1); // 1
echo test(); // 100
```

12. 

배열을 만드는 방법은 `array()`, `[]` 로 구성되어있고

5.4버전 이후 부터 `[]` 를 지원한다.

```php
$array1 = array("11","22");
$array2 = ["11", "22"];
```

기본적으로 배열 안에 인덱스를 가져오는 것은 java와 동일하고,

5.4버전 이상부터는 다음과 같이 가능하다.

```php
function test() {
return ["22", "33", "44"];
}

echo test()[1]; // "33"
```

13. 배열을 제어할 수 있다 

```php
// 맨앞에 추가
array_unshift(),
push(),
pop(),
shift(), 
merge(), 
sort(),
rsort()
```

14. 연관배열 → 배열안에 Object를 넣는다고 생각해야할거같다.

```php
// key, value 형식으로 보자.
$array = [ 'a' => 10, 'b' => 4, 'c' => 15 ];
$array['a']; = 10
```
