
- `OOP` - 클래스를 가지고 프로그래밍 하는 것

![OOP](https://github.com/awse2050/TIL/blob/main/Flutter/Dart/img/12312.PNG)


### 클래스의 선언

```jsx
void main() {

  Idol idol = Idol("홍길동", 17); // new Idol(..);
// const 생성자(플러터에서 약간의 효율을 늘려줌)
  Idol idol2 = const Idol(...); 

// const 생성자를 만들었어도 const 키워드를 붙이지 않고
// 생성할 경우 일치하지 않는다
  print(idol == idol2);

  idol.printIdol();
  
}
class Idol {
  
	final String name;
  final int age;
  
  // 기본 생성자 생성 방법
	// const 를 붙이면 생성자 선언시 const 를 붙여서 만들 수 있다.
// Idol({required this.name, required this.age});
 const Idol(String name, int age) 
    : this.name = name, this.age = age;
  
  // Named Constructor
  Idol.of(List values) 
     : this.name = values[0],
      this.age = values[1];
  
  
  void printIdol() {
    print(' name : $name , age = $age ');
  }
  
}
```

- `const` 생성자

기본적으로 같은 클래스에서 생성된 인스턴스는 다르게 본다.

하지만 `const` 로 생성한 인스턴스의 경우 같은 데이터로 보게된다.  만약 `const` 로 생성자를 만들어 뒀어도 인스턴스 생성시에 `const`를 붙이지 않으면 `const` 생성자로 인식을 하지 않는다.

### Getter , Setter

`get` 키워드를 사용해서 `getter` 를 만들 수 있으며 몸통의 경우 메서드형태가 아닌 `{}` 중괄호를 사용해서 만들어 쓴다.

```jsx
// 어떤 값을 반환하는 지 알려주기 위해서 타입을 선언한다.
String get firstmember {
	// 반환하고 싶은 값을 넣는다.
	return this.members[0];
}

print(blackPink.firstMemeber);
```

`set` 키워드를 사용해서 `setter` 를 만들 수 있다.

해당 메서드에는 단 하나의 매개변수만 들어간다.

```jsx
set firstMember(String name) {
	this.members[0] = name;
}
// Setter 를 사용하는 방법.
blackPink.firstMember = "코드팩토리";
```

### private

`private` 변수는 사실 설명하기 힘들다.

`private` 변수는 이 파일 밖에서 사용할수 없는 값이다.

사용방법은 클래스 명칭앞에 `_` 를 붙인다.

### 상속

상속을 받으면 부모클래스의 모든 속성을 자식클래스가 받는다.

```jsx
class Idol {
	String name;
	int membersCount;

	Idol({
		required this. name,
		required this.membersCount,
	});

	void sayName() {
		print('저는 ${this.name}입니다.');
	}

	void sayMembersCount() {
		print('${this.name}은 ${this.membersCount}명');
	}
}
// 자식
class BoyGroup extends Idol {
		BoyGroup(String name, int memberCount)
			//Named Parameter 이므로 이름을 넣어서 호출
			: super(name: name, memberCount: memberCount)
}

```

### 오버라이딩

`Dart` 에서도 `Override` 를 지원한다.

```jsx
class Idol {
  
  String name;
  
  Idol({required this.name});

  // 자바와 다르게 소문자로 선언한다.
  @override
  String toString() {
      return "ss";
  }
}
```

### static

```jsx
class Employee {
	// static 은 인스턴스 생성없이 호출가능하므로
	// 해당 변수가 null일 수 있다.
	static String? building;
	final String name;
}
Employee.building = "???";

```

### Interface

`Dart` 에서는 `interface` 또한 클래스명칭으로 생성한다.

```jsx
class IdolInterface {
	String name;
	
	IdolInterface(this.name);
		
	void sayName() {}
}

class BoyGroup implements IdolInterface {
	String name;

	BoyGroup(this.name);

	void sayName(){}

}
```

이 때, 인터페이스는 인스턴스를 생성하지 않는 의도로 만들었는데, 인스턴스 생성이 되는 상황이다. 이 럴땐 `abstract` 를 붙여서 인스턴스화를 불가능하게 만들 수 있다.

```jsx
abstract class IdolInterface {}
```

### Generic

타입을 외부에서 받을 때 사용한다. `Java` 에서 사용하는 방식과 비슷하다. 클래스 이름 옆에 `<>` 를 사용한다.

```jsx
class Lecture<T> {
  final T name;
  
  Lecture(this.name);
}
```
