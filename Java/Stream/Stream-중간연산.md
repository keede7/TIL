# 스트림의 중간연산

### **스트림 자르기 - skip(), limit()**


`skip(long i)` -> 처음의 i개 요소를 건너뛴다.

`limit(long i)` -> i개 까지의 스트림 요소를 제한한다.

```java
IntStream intStream = IntStream.rangeClosed(1, 10);
        intStream.skip(3).limit(5).forEach(System.out::println);
```

### **스트림 요소 걸러내기 - filter(), distinct()**

`filter(Predicate<? super T> predicate)` - 주어진 조건에 맞지 않는 요소를 걸러낸다.

`distinct()` - 스트림에서 중복된 요소를 제거한다.

```java
IntStream intStream = IntStream.of(1, 2, 3, 4, 4, 1, 1, 2, 3, 5, 6, 7, 5, 5);
intStream.distinct().forEach(System.out::println);

System.out.println();

IntStream.rangeClosed(1, 10).filter(n -> n%2 ==0).forEach(System.out::println);
```

### **스트림 정렬하기**

`sort()`의 매개변수로 직접 정렬순서를 정할 수 있으며 없을 경우 기본으로 적용되어 있는 순서를 적용시킨다.

아래는 적용할 수 있는 정렬타입이다.

```java
Comparator.naturalOrder();
(s1, s2) -> s1.compareTo(s2)
String::compareTo;

Comparator.reverseOrder();
Comparator.<String>naturalOrder().reversed();
String.CASE_INSENSITIVE_ORDER;
String.CASE_INSENSITIVE_ORDER.reversed();

Comparator.comparing(String::length);  // 길이 순 정렬
Comparator.comparingInt(String::length); // 오토박싱

Comparator.comparing(String::length).reversed();
```

가장 기본적으로 많이 사용하는 메서드는 `comparing()` 이다.

```java
comparing(Function<T, R> f);
comparing(Function<T, R> f, Comparator<U> c);
```

스트림의 요소가 `Comparable` 을 구현한 경우, 매개변수 하나짜리를 사용하면 되고 그렇지 않은 경우, 추가적인 매개변수로 정렬기준을 정해야 한다.

```java
comparingInt(ToIntFunction<T> f);
comparingLong(ToLongFunction<T> f);
comparingDouble(ToDoubleFunction<T> f);
```

비교 대상이 **기본형**이라면 `comparing()` 대신에 위 메서드를 사용하면 **오토박싱과 언박싱과정**이 없어서 효율적이다. 그리고 정렬조건은 `thenComparing()` 을 쓴다.

```java
thenComparing(Comparator<T> other);
thenComparing(Function<T, R> f);
thenComparing(Function<T, R> f, Comparator<T> other);
```

예를 들어서, 반별, 성적순, 이름순 으로 정렬하면 다음과 같다.

```java
studentStream.sorted(Comparator.comapring(Student::getBean)
								.thenComparing(Student::getTotalScore)
								.thenComparing(Student::getName)
								.forEach(System.out::println);
```

### 변환 - map()

스트림의 요소에 저장된 값 중에서 원하는 필드를 뽑거나 특정 형태로 변환할 때 쓰인다. 매개변수로 T타입을 R타입으로 변환해서 반환하는 함수를 지정해야 한다.

```java
Stream<R> map(Function<? super T, ? extends R> mapper);
```

```java
File[] fileArr = {
      new File("Ex1.java"),
      new File("Ex1.bak"),
      new File("Ex2.java"),
      new File("Ex1"),
      new File("Ex1.txt")
};

Arrays.stream(fileArr).map(File::getName)
        .forEach(System.out::println);

System.out.println();

Arrays.stream(fileArr).map(File::getName)
        .filter(s -> s.indexOf(".") != -1)
        .map(s -> s.substring(s.indexOf(".")+1))
        .map(String::toUpperCase)
        .distinct()
        .forEach(System.out::println);
```

### mapToInt(), mapToLong(), mapToDouble()

`map()` 의 경우 `Stream<T>` 형태로 반환하는데 스트림의 요소를 숫자로 변환하는 경우에는 `IntStream` 과 같은 **기본형 스트림**으로 변환하는 것이 더 유용하다.

```java
DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper);
IntStream mapToInt(ToIntFunction<? super T> mapper);
LongStream mapToLong(ToLongFunction<? super T> mapper);
```

`IntStream` 와 같은 **기본형 스트림**들은 다음과 같은 메서드들이 지원된다.

```java
int sum(); // 모든 요소의 합
OptionalDouble average() //  sum() / count()
OptionalInt max() // 스트림 요소 중 가장 큰 값
OptionalInt min() // 스트림 요소중 제일 작은 값.

```

해당 메서드들은 **최종 연산**이기 때문에 호출 후에 스트림이 닫힌다는 점을 주의해야 한다. 아래 처럼 연속해서 호출이 불가능하다.

```java
IntStream scoreStream = studentStream.mapToInt(Student::getTotalScore);
long totalScore = scoreStream.sum(); // 스트림이 닫힌다.
OptionalDouble average = scoreStream.average() // 에러발생
```

만약 `sum()` 과 `average()` 를 모두 호출해야 할 때, 스트림을 또 생성하기 불편하므로 `summaryStatistics()` 메서드를 제공한다.

```java
  IntSummaryStatistics stat = scoreStream.summaryStatistics();
  long totalCount = stat.getCount();
  long totalScore = stat.getSum();
  double avgScore = stat.getAverage();
```

`IntSummaryStatistics` 는 위와 같이 다양한 종류의 메서드를 제공한다.

기본형 스트림인 `LongStream` 과 `DoubleStream`도 같은 연산을 지원한다.

기본형 스트림을 다시 `Stream<T>` 로 변환할 떄는 `mapToObj()` 를 사용하고

`Stream<Integer>` 로 변환시에는 `boxed()` 를 사용한다.

```java
Stream<Student> studentStream = getSampleStudentStream();
    
IntStream intStream = studentStream.mapToInt(Student::getTotalScore);

// 여러번의 스트림을 사용하기 위해서 summaryStatistics로 변환
IntSummaryStatistics intSummaryStatistics = intStream.summaryStatistics();
long count = intSummaryStatistics.getCount();
double average = intSummaryStatistics.getAverage();
long sum = intSummaryStatistics.getSum();

System.out.println("count : " + count);
System.out.println("average : " + average);
System.out.println("sum : " + sum);
```

### flatMap() - Steam<T[]> 를 Stream<T>로 변환

예를 들어 스트림 타입이 `Stream<T[]>` 인 경우 `Stream<T>` 로 다루는 것이 더 편리할 때 가 있다. 이떄는 `map()` 대신 `flatMap()` 을 쓰는게 좋다.

```java
String[] lineArr = {
		"Belive",
		"Do or do"
};

Stream<String> lineStream = Arrays.stream(lineArr);
Stream<Stream<String>> strArrStream = lineStream.map(lint -> Stream.of(line.split(" +")));
```

`map()` 이  `Stream<String>` 이 아니라 `Stream<Stream<String>>` 을 결과로 돌려준다. 이럴 때 `flatMap()` 으로 사용해서 원하는 결과를 얻을 수 있다.
