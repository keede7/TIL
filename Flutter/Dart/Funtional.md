### 기본적인 값 변환 방법

```jsx
List<String> list = ["", ""];
list.asMap() // Map형태로 변환
list.toSet() // Set형태로 변환

Map map = list.asMap();

map.keys.toList() // key값들로 list를 만든다
map.values.toList() // value 값들로 list를 만든다.

Set set = Set.from(list);

set.toList() // Set을 list로 변환
```

### map()

`Java` 의 `Stream` 기능과 동일하다.

`Parameter` 의 `()` 괄호는 생략할 수 는 없다.

```jsx
List<String> list = ["a","b", "c"];

// 새로운 list를 만들어낸다.
final list2 = list.map( (x) {
	return ' x$x';
});

// 화살표 함수 응용
list.map( (x) => 'x$x');

String number = "12345";

final list3 = number.split('').map( (x) => '$x.jpg').toList();

```

### Map 타입으로 반환하는 방법

`MapEntry` 를 사용해서 반환한다.

```jsx
Map<String, String> maps = {
    "안녕하세요": "1212",
    "하이": "3434",
  };

  final Map<String, String> convertMap = 
    maps.map((key, value) => MapEntry('hihi$key', 'ihih$value'));
  
  print(convertMap);// {hihi안녕하세요: ihih1212, hihi하이: ihih3434}
```

### Set 타입의 값에 사용하는 방법 예시

```jsx
Set blackSet =  {
	"로제",
	"지수",
	"제니",
	"리사"
}

final newSet = blcackSet.map((x)=>'블랙핑크 $x').toSet();
print(newSet);
```

### where()

작동방식은 `map()` 과 같고, 함수를 받으며 하나의 파라미터를 받는다. 대상 `Map` 을 전부 돌면서 `true`, 또는 `false`를 받는다.

`true`면 값을 유지하고 `false`면 삭제해버린다. **일종의 `filter` 역할을 한다.**

```jsx
List<Map<String,String>>g people = [
	{
		'name': '로제',
		'group' : '블핑',
	},
	{
		'name':'탑',
		'group':'big',
	}
]

// group이 '블핑'인 데이터만 남는다.
people.where( (x) => x['group']=='블핑' ).toList();

```

### Reduce

2개의 `Parameter` 를 받으며 첫 번째 `Parameter` 가 해당 배열의 첫번째 값 역할을 하고, 두 번째 `Paarmeter`가 해당 배열의 2번쨰 값 역할을 하며

이후 연산에서는 처음에 연산하여 반환된 값과 그 다음 인덱스값(3)과 비교해서 연산을 진행한다.

```jsx
List<int> members = [
1,2,3,4,5
];

numbers.reduce( (prev.next) { 
	return prev + next;
 });

```

`reduce`는 절대적인 규칙이 있다.

각 반환되는 값들의 타입이 항상 더해지는 값들의 타입과 동일해야한다는 것이다. 즉, `reduce`를 대상으로 되는 값들의 타입과 `return` 값 타입이 동일해야 한다.

이 단점을 보완한 기능이 `fold()` 이다.


### fold()

`reduce` 의 단점을 보완한 기능이다. 

`fold` 는 2개의 파라미터를 받는다.

```jsx
List<int> numbers = [1,3,5,6,7];

// 대신 어떤값이 리턴될것인지 정해줘야한다.
// fold<type>
final sum =
numbers.fold<int>(0, (prev,next) => prev+next);

```

`reduce`의 경우 초기값 자체가 `prev` 가 되어서 초기값을 선언하기 어려웠지만 `fold` 의 경우 초기값을 미리 설정해둘수 있다.

2개의 파라미터로 받지만 최초에는 초기값과 `next` 값 (첫번쨰 인덱스값[1])으로 계산을 먼저 진행하게 된다.


### Cascading Operator

여러 배열을 뿌려주는 역할을 한다.

`JS`에서 `spread`과 비슷하다.

```jsx

List<int> even = [
2,4,6,8];

List<int> odd = [ 1,3,5,7];

print([even,odd]); // [[2,4,6,8], [1,3,5,7]];
print( [...even, ...odd ] ); // [2,4,6,8,1,3,5,7]
// [...even] 또한 새로운 배열로 생성된다.
print(even == [...even]) // false
```

### 실제 상황에서의 예시


```dart
void main() {

  // 더미 리스트 생성
  List<Map<String, String>> idols = [
    {
      "name": "하이",
      "group": "HI",
    },
    {
      "name":"헬로우",
      "group":"HI",
    },
    {
      "name":"바이",
      "group":"BYE",
    },
    {
      "name":"잘가",
      "group":"BYE",
    }    
  ];
  
  print(' idols : $idols');
  
  final List<Person> persons = idols.map( (idol) => Person(
    // ! 를 쓰는 이유는 해당 Map에 키값이 있을지 없을지 모르기 떄문에
    // 확정적으로 있다고 가정을 시켜주기 위해서 사용한다.
    name: idol['name']!,
    group: idol['group']!,
  )) // 그룹이 HI인 사람들만 가져온다.
  .where( (person) => person.group == 'HI')
    .toList();
  
  print('persons : $persons');
  
  for( Person person in persons) {
    print('person : $person');
  }
  
}

class Person {
  
  final String name;
  final String group;
  
  Person({
    required this.name,
    required this.group,
  });
  
  @override
  String toString() {
    return '그룹명은 $group 이고, 이름은 $name 입니다.';
  }
  
}
```

