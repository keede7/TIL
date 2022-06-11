## 스트림의 최종연산

**최종연산**은 스트림의 요소를 소모해서 결과를 만들어 낸다.

그래서 **최종연산** 이후에는 스트림이 닫히게 되고 더이상 사용할 수 없다.

**최종연산**의 결과는 스트림 요소의 합과 같은 단일값이거나, 스트림의 요소가 담긴 **배열의 컬렉션**일 수 있다.

### forEach()

`peek()` 과 다르게 스트림의 요소를 소모하는 최종연산이다. 

반환 타입이 `void` 이므로 스트림의 요소를 출력하는 용도로 많이 쓰인다.

```java
void forEach(Consumer<? super T> action)
```

### 조건검사 - allMatch(), anyMatch(), noneMatch(), findFirst(), findAny();

스트림의 요소에 대해 지정된 조건에 모든 요소가 일치하는, 일부가 일치하는지 아니면 어떤 요소도 일치하지 않는지 확인하는데 사용할 수 있는 메서드들 이다. 이 메서드들은 모두 매개변수로 `Predicate` 를 요구하며 연산결과로 `boolean` 을 반환한다.

만약 총점이 100 이하인 학생을 반환한다면 다음과 같다.

```java
boolean noFailed = stuStream.anyMatch(s -> s.getTotalScore() <= 100)
```

스트림의 요소 중에서 조건에 일치하는 첫번째 것을 반환하는 `findFirst()` 가 있는데, 주로 `filter()` 와 함께 사용되어 조건에 맞는 스트림의 요소가 있는지 확인하는 데 사용한다. **병렬 스트림**인경우 `findFirst()` 대신 `findAny()` 를 사용해야 한다.

```java
Optional<Student> stu = stuStream.filter(s -> s.getTotalScore <= 100).findFirst();

Optional<Student> stu = parallelStream.filter(s->s.getTotalScore()<-=100).findAny();
```

### 통계 - count, sum, average, max, min()

`IntStream` 과 같은 기본형 스트림에는 스트림의 요소들에 대한 통계 관련 정보를 얻을 수 있는 메서드들이 있다. 

만약 기본형 스트림이 아닌 경우라면 아래의 메서드만 가진다.

```java
long count()
Optional<T> max(Comparator<? super T> action);
Optional<T> min(Comparator<? super T> action);
```

### 리듀싱 - reduce()

`reduce()` 이름에서 짐작할 수 있듯이, 스트림의 요소를 줄여나가면서 연산을 수행하고 최종결과를 반환한다. 그래서 매개변수의 타입이 `BinaryOperator<T>` 인 것이다. 처음 두 요소를 가지고 연산한 결과를 가지고 그 다음요소와 연산한다.

이 과정에서 스트림의 요소를 하나씩 소모하게되어, 모든 요소를 소모할 경우에 그 결과를 반환한다. 이외에도 연산결과의 초기값을 가지는 `reduce()` 도 있는데, 이 메서드들은 초기값과 스트림의 첫번째 요소로 연산을 시작한다.

스트림의 요소가 하나도 없을 경우에는 초기값이 반환되어 반환타입이 `Optional<T>` 가 아니라 `T` 이다.

```java
T reduce(T identity, BinaryOperator<T> bo);
U reduce(U identity, BiFunction<U,T,U> bf, BinaryOperator<U> com);
```

사실 통계연산들은 대부분 내부적으로 모두 `reduce()` 를 사용해서 아래와 같이 작성된 것이다.

```java
int count = intStream.reduce(0, (a,b) -> a+1);
int sum = intStream.reduce(0, (a,b) -> a+b);
int max = intStream.reduce(Integer.MIN_VALUE, (a,b) -> a>b ? a:b);
int min = intStream.reduce(Integer.MAX_VALUE, (a,b) -> a<b ? a:b);
```

여기서 `min()` 과 `max()` 는 초기값이 필요없는 부분이라서 매개변수 하나짜리 `reduce()` 를 사용하는 것어 더 낫다. 대신 반환타입은 `OptionalInt` 로 사용을 해야 한다.

```java
OptionalInt max = intStream.reduce( (a,b) -> a > b ? a : b);
OptionalInt min = intStream.reduce( (a,b) -> a < b ? a : b);
```

메서드 참조로 바꿀 경우에는 다음과 같다.

```java
OptionalInt max = intStream.reduce(Integer::max);
OptionalInt min = intStream.reduce(Integer::min);
```

---

### collect()

스트림의 **최종연산** 중에서 가장 복잡하면서 유용하게 활용될 수 있는 것이다.

`collect()` 는 스트림의 요소를 수집하는 **최종연산**으로 **리듀싱**과 유사하다.

`collect()` 가 스트림의 요소를 수집하려면, 어떻게 수집할 것인가에 대한 방법이 정의되어 있어야 하는데, 이 방법은 정의한것이 컬렉터이다.

컬렉터는 `Collector` 인터페이스를 구현한 것으로 직접 구현할 수 도 있고 미리 작성된 것을 사용할 수도  있다.

```java
collect()  스트림의 최종연산, 매개변수로 컬렉터를 필요한다.
Collector 인터페이스, 컬렉터는 이 인터페이스를 구현해야 한다.
Collectors 클래스, static메서드로 미리 작성된 컬렉터를 제공한다.
```

### 스트림 → 컬렉선&배열로 변환  - toList, toSet, toMap, toCollection, toArray()

스트림의 모든 요소를 컬렉션에 수집하려면, `Collectors` 클래스의 `toList` 와 같은 메서드를 사용하면 된다. `List`, `Set`이 아닌 특정 컬렉션을 지정하려면, `toCollection()` 에 해당 컬렉션의 생성자 참조를 매개변수로 넣어주면된다.

```java
List<String> names = stuStream.map(Student::getName).collect(Collectors.toList());
ArrayList<String> list = names.stream().collect(Collectors.toCollection(ArrayList::new));

```

`Map` 은 키와 값을 쌍으로 저장해야하므로 객체의 어떤 필드를 키로 사용할지와 값으로 사용할지를 결정해야 한다.

```java
Map<String, Person> map = personStream.collect(Collectors.toMap(p->p.getRegId()p, p->p);
```

여기서 `p->p` 인 항등함수 대신 `Function.identity()` 를 쓸수 있다.

스트림의 저장요소들을 `T[]` 타입의 배열로 변환하려면, `toArray()` 를 쓰면 된다. 해당 타입의 생성자 참조를 매개변수로 지정해줘야 한다.

만일 매개변수를 지정하지 않으면 반환되는 배열 타입은 `Object[]` 이다.

```java
Student[] stuNames = studentStream.toArray(Student[]::new);
Student[] stuNames = studentStream.toArray(); // 에러
Object[] stuNames = studentStream.toArray();
```

### 통계 - counting(), sumingInt(), averagingInt(), maxBy(), minBy()

최종연산들이 제공하는 통계정보를 `collect()` 를 통해서도 얻을 수 있다.

나중에 `groupingBy()` 와 함께 쓰일 수 있어서 필요한 부분이 있다.

```java
Stream<Student> sampleStudentStream = getSampleStudentStream();
      
//long count = sampleStudentStream.count();
long count = sampleStudentStream.collect(counting()); // count()와 같다.

//long totalScore = sampleStudentStream.mapToInt(Student::getTotalScore).sun();
long totalScore = sampleStudentStream.collect(summingInt(Student::getTotalScore));

//Optional<Student> collect = sampleStudentStream.max(Comparator.comparingInt(Student::getTotalScore);
Optional<Student> collect = sampleStudentStream.collect(maxBy(Comparator.comparingInt(Student::getTotalScore)));

// summingInt 과 혼동주의
// sampleStudentStream.mapToInt(Student::getTotalScore).summaryStatistics();
IntSummaryStatistics collect1 = sampleStudentStream.collect(summarizingInt(Student::getTotalScore));

```

### 리듀싱 - reducing()

리듀싱 역시 `collect()` 로 가능하다. `IntStream` 에는 매개변수 3개짜리 `collect()` 만 정의되어 있다. 따라서 `boxed()` 를 통해서 `Stream<Integer` 로 변환을 시켜야 매개변수 1개의 `collect()` 를 쓸 수 있다.

```java
IntStream intStream = new Random().ints(1, 46).distinct().limit(6);

OptionalInt mnx = intStream.reduce(Integer::max);
Optional<Integer> max1 = intStream.boxed().collect(reducing(Integer::max));

int sum = intStream.reduce(0, (a, b) -> a + b);
Integer collect = intStream.boxed().collect(reducing(0, (a, b) -> a + b));

Stream<Student> sampleStudentStream = getSampleStudentStream();
//        int reduce = sampleStudentStream.map(Student::getTotalScore).reduce(0, Integer::sum);
// (초기값, map, 리듀싱작업)
Integer reduce = sampleStudentStream.collect(reducing(0, Student::getTotalScore, Integer::sum));

```

마지막 리듀싱은 `map()` 과 `reduce()` 를 하나로 합쳐놓은 것이다.

### 문자열 결합 - joining()

**문자열 스트림**의 모든 요소를 하나의 문자열로 연결해서 반환한다. 구분자를 지정해 줄수도 있고, **접두사나 접미사도 지정가능하다.**

스트림의 요소가 `String` 이나 `StringBuffer` 처럼 `CharSequence` 의 자손인 경우에만 결합이 가능하므로 스트림의 요소가 문자열이 아닌 경우에는 먼저 `map()` 을 이용해서 스트림의 요소를 문자열로 변환해야 한다.

```java
// 문자열을 그저 합침.
String collect1 = sampleStudentStream.map(Student::getName).collect(joining());
// 문자열 사이에 , 를 붙인다.
String collect2 = sampleStudentStream.map(Student::getName).collect(Collectors.joining(","));
// 문자열 사이에 ,를 붙이며 맨 앞에 [ 를, 맨 뒤에 ] 를 붙인다.
String collect3 = sampleStudentStream.map(Student::getName).collect(joining(",", "[", "]"));
System.out.println("collect3 : " + collect3);
```

### 그룹화 및 분할 - groupingBy(), partitioningBy()

그룹화는 스트림의 요소를 특정 기준으로 그룹화 하는 것을 의미하고, 분할은 스트림의 요소를 두 가지, 지정조건에 일치하는 그룹과 일치하지 않는 그룹으로 분할을 의미한다.

`groupingBy()` 는 스트림의 요소를 `Function` 으로 `partitioningBy()` 는 `Predicate` 로 분류한다.

```java
Collector groupingBy(Function f);
Collector groupingBy(Function f, Collector c);
Collector groupingBy(Function f, Supplier s, Collector c);

Collector partitioningBy(Predicate p);
Collector partitioningBy(Predicate p, Collector c);
```

두개의 차이는 분류를 `Function` 으로 하는가, `Predicate` 로 하는 가에 차이만 있을 뿐이며 동일하다.  

스트림을 2개의 그룹으로 나눠야 한다면, 당연히 `partitioningBy()` 로 분할하는 것이 더 빠르다.

그 외에는 `groupingBy()` 를 쓰면 된다. 그룹화 결과는 `Map` 으로 반환된다.

**partitioningBy() 에 의한 분류**

기본적인 분할로 학생들을 성별로 나누어서 `List` 로 담는다.

```java
// 기본 분할
Map<Boolean, List<Person>> collect = getStream()
      .collect(Collectors.partitioningBy(Person::isMale));
List<Person> man = collect.get(true);
List<Person> female = collect.get(false);
// 기본 분할 + 통계 정보
Map<Boolean, Long> collect1 = getStream()
      .collect(Collectors.partitioningBy(Person::isMale, Collectors.counting()));

Long manCount = collect1.get(true);
Long femaleCount = collect1.get(false);
```

`counting()` 대신에 `summingLong()` 을 사용했다면, 남학생과 여학생의 총점을 구할 수 있다.

남학생 1등과 여학생 1등을 구하는 방법

```java
// 점수로 남학생 1등과 여학생 1등
Map<Boolean, Optional<Person>> collect2 = getStream().collect(
      Collectors.partitioningBy(Person::isMale,
      Collectors.maxBy(Comparator.comparingInt(Person::getScore)))
);

System.out.println("남자 1등 : " + collect2.get(true).get());
System.out.println("여자 1등 : " + collect2.get(false).get());
```

`maxBy()` 타입은 `Optional` 타입이라서 위 같은 결과가 나왔는데 `Optional` 타입이 아니라 `T` 타입으로 나오게 하려면

`collectingAndThen()` 을 써서 `Optional::get` 으로 함께 사용하면 된다.

```java
// 점수로 남학생 1등과 여학생 1등
Map<Boolean, Optional<Person>> collect2 = getStream()
  .collect(
          partitioningBy(Person::isMale,
                  maxBy(Comparator.comparingInt(Person::getScore))
          )
  );
// 같은 결과지만 반환타입이 다르다.
Map<Boolean, Person> collect3 = getStream()
.collect(
  partitioningBy(Person::isMale,
          collectingAndThen(
                  maxBy(Comparator.comparingInt(Person::getScore)),
                  Optional::get)
  )
);
```

성적이 150점 아래인 학생들은 불합격자를 성별로 분류하려면 `partitioningBy()` 를 한번더 사용해서 이중분할을 해야한다.

```java
Map<Boolean, Map<Boolean, List<Person>>> collect = getStream()
        .collect(
                partitioningBy(Person::isMale,
                        partitioningBy(s -> s.getScore() < 150)
                )
        );

List<Person> man = collect.get(true).get(true);
List<Person> female = collect.get(false).get(true);
```

### groupingBy() 에 의한 분류

`groupingBy()` 를 사용하면 기본적으로  `List<T>` 에 담는다.  만약 원한다면 `toList()` 대신에 `toSet()` 이나 `toCollection(HashSet::new)`  를 사용할 수도 있다. 단 `Map` 의 지네릭 타입도 적절히 변경해줘야 한다.

```java
Map<Integer, List<Person>> collect = getStream()
          .collect(groupingBy(Person::getBan));
// 다른 타입으로 변환
Map<Integer, List<Person>> collect1 = getStream()
        .collect(groupingBy(Person::getBan, toList())); // toList 생략가능

	Map<Integer, HashSet<Person>> collect2 = getStream()
	.collect(groupingBy(Person::getBan, toCollection(HashSet::new)));
```

성적 등급으로 분류화 할 경우

```java
Map<Person.Level, Long> collect = getStream().collect(
        groupingBy(p -> {
                    //Person::filterLevel
                    if (p.getScore() >= 200) {
                        return Person.Level.HIGH;
                    } else if (p.getScore() >= 100) {
                        return Person.Level.MID;
                    } else {
                        return Person.Level.LOW;
                    }
                }, counting())
);
```

만약 `groupingBy()` 를 여러번 쓰고 싶을 경우, 다수준 그룹화가 가능하다.

학년별로 그룹화를 한 후 다시 반별로 그룹화 하고 싶다면?

```java
Map<Integer, Map<Integer, List<Person>>> collect = 
				getStream().collect(
                groupingBy(Person::getHak,
                        groupingBy(Person::getBan))
        );

```
