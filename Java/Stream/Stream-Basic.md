## Stream

우리가 수 많은 데이터를 다룰 때, `컬렉션`이나 `배열`에 데이터를 담고 원하는 결과를 얻기 위해 `for문` 과 `Iterator` 를 이용해서 코드를 작성했다.

이러한 방식으로 작성된 코드는 너무 길고 알아보기 어렵다. 그리고 **재사용성도 떨어진다.**

`Collection` 이나 `Iterator`와 같은 인터페이스를 이용해서 컬렉션을 다루는 방식을 표준화하기는 했지만, 

각 컬렉션 클래스에는 같은 기능의 메서드들이 중복해서 정의되어 있다. 

이러한 문제점들을 해결하기 위해서 만든 것이 `Stream` 이다.

스트림은 데이터소스를 추상화하고, 데이터를 다루는데 자주 사용되는 메서드들을 정의해 놓았다. 

데이터소스를 추상화 했다는 것은, 데이터 소스가 무엇이든 간에 같은 방식으로 다룰수 있게 되었다는 것과 **코드의 재사용성이 높아진다는 것**을 의미한다.


### Stream 생성방법

```java
  // String 배열을 선언.
  String[] strArr = {"aaa", "bbb" , "ccc"};
  // 컬렉션으로 변환
  List<String> strList = Arrays.asList(strArr);

  // 스트림을 생성하는 방법.
  Stream<String> stream1 = strList.stream();
  Stream<String> stream2 = Arrays.stream(strArr);
```

스트림의 데이터소스를 읽어서 정렬하고 화면에 출력한다고 실제 데이터소스가 변경되는 것은 아니다.

```java
  Arrays.sort(strArr);
  Collections.sort(strList);

  for( String str : strArr ) {
    System.out.println(str);
  }

  for ( String str : strList) {
    System.out.println(str);
  }
```

### 스트림은 데이터 소스를 변경하지 않는다.

스트림은 데이터소스로부터 데이터를 읽기만 할 뿐, 데이터 소스를 변경하지 않는다는 차이점이 있다.

정렬된 결과를 `컬렉션`이나 `배열`에 담아서 반환할 수 도 있다.


### 스트림은 일회용 이다.

스트림은 `Iterator` 처럼 일회용이다. `Iterator`로 컬렉션의 요소를 모두 읽고 나면 다시 사용할 수 없는 것처럼, 

스트림도 한번 사용하면 닫혀서 다시 사용할 수 없다. 필요하다면 다시 **스트림을 재생성**해야 한다.

### 스트림은 작업을 내부 반복으로 처리한다.

스트림이 간결한 작업을 할 수 있는 비결 중 하나가 **내부 반복**이다.

내부 반복이라는 것은 반복문을 메서드 내부에 숨길 수 있다는 것이다. 

`forEach()` 는 스트림에 정의된 메서드 중 하나로 매개변수에 대입된 람다식을 데이터 소스의 모든 요소에 적용된다.


### 스트림 연산

스트림이 제공하는 다양한 연산을 이용해서 복잡한 작업들을 간단히 처리할 수 있다. 

스트림이 제공하는 연산은 **중간 연산**과 **최종 연산**으로 분류할 수 있는데, 

중간연산은 연산결과를 스트림으로 반환하기 때문에 **중간 연산**을 **연속해서 연결**할 수 있다.

```jsx
stream.distinct().limit(5).sorted().forEach(System.out::println);
```

- `distinct()` : 중복 제거
- `filter(Predicate<T> predicate)` : 조건에 안맞는 요소 제외
- `limit(long maxSize)` : 스트림의 일부를 자른다.
- `skip(long n)` : 스트림의 일부를 건너뛴다.
- `peek(Consumer<T> action)` : 스트림의 요소에 작업수행
- `sorted(), sorted(Comparator<T> comparator)` : 요소를 정렬한다.

등등이 있으며, **중간 연산**은 `map()` , `flatMap()` , **최종 연산**은 `reduce()` , `collect()` 가 핵심이다. 나머지는 이해하기 쉽고 간단하다.

### 지연된 연산

스트림 연산에서 중요한 점은 **최종 연산**이 수행되기 전까지는 **중간 연산**이 수행되지 않는다는 점이다. 스트림에 대해 `distinct()` 나 `sort()` 같은 **중간 연산**을 호출해도 **즉각적인 연산이 수행되는 것은 아니다**. **중간 연산**을 호출하는 것은 단지 **어떤 작업이 수행되어야 하는지를 지정해주는 것 뿐**이다.

**최종 연산**이 수행되어야 비로소 스트림의 요소들이 **중간 연산**을 거쳐서 **최종연산**에서 소모된다,

### Stream<Integer> , IntStream

요소의 타입이 T인 스트림은 기본적으로 **Stream<T>** 이지만.오토박싱&언박싱으로 인한 비효율을 줄이기 위해 데이터 소스의 요소를 기본형으로 다루는 스트림인 `IntStream` , `LongStream` , `DoubleStream` 이 제공된다.

일반적으로 Stream<Integer> 대신 IntStream 을 사용하는  것이 더 효율적이고, `IntStream` 에는 `int`타입의 값으로 작업하는 데 유용한 메서드들이 포함되어 있다. 

### 병렬 스트림

스트림으로 데이터를 다룰 떄 장점 중 하나가 바로 **병렬 처리**가 쉽다는 것이다. 병렬 스트림은 내부적으로 이 프레임워크를 이용해서 자동적으로 연산을 병렬로 수행한다.

스트림에 `parallel()` 이라는 메서드를 호출해서 병렬로 연산을 수행하도록 지시하면 될 뿐이다. 반대로 **병렬**로 처리되지 않게 하려면 `sequential()` 을 호출하면된다. 사실 모든 스트림은 **기본적으로** **병렬 스트림이 아니므로** 굳이 호출하지 않아도 된다. 그저 변경된 스트림을 바꿀때 호출하면 된다.

```jsx
int sum = strStream.parallel().mapToInt(s -> s.length()).sum();
```

### 배열

배열을 소스로 하는 스트림을 생성하는 메서드는 다음과 같이 `Stream` 과 `Arrays` 에 `static 메서드` 로 정의되어 있다.

```jsx
Stream<T> Stream.of(T... values);
Stream<T> Stream.of(T[]);
Stream<T> Arrays.stream(T[]);
Stream<T> Arrays.stream(T[] array, int startInclusive, int endExclusive);
```

문자열 스트림의 경우 다음과 같다.

```jsx
Stream<String> strStream = Stream.of("a", "b", "c");
Stream.of(new String[] {"a", "b", "c"});
Arrays.stream(new String[] {"a", "b", "c"});
```

### 임의의 수

난수를 생성하는 데 사용하는 `Random` 클래스를 생성해서 다음과 같이 스트림을 만들 수 있다. 이 때 `limit` 을 줘서 스트림의 크기에 제한을 줘야 한다.

```jsx
IntStream ints = new Random().ints();
LongStream longs = new Random().longs();
DoubleStream doubles = new Random().doubles();

// 무한스트림에 범위 제한 주기.
ints = new Random().ints(5);
ints = new Random().ints(1,5); // 5는 포함하지 않는다.
```

### 람다식 - iterate(), generate()

스트림 클래스의 iterate(), generate()는 람다식을 매개변수로 받아서, 이 람다식에 의해 계산되는 값들을 요소로 하는 무한 스트림을 생성한다.

- **iterate()** :  씨앗값으로 지정된 값부터 시작해서, 람다식 f에 의해 계산된 결과를 다시 seed값으로 해서 계산을 반복한다. 아래의 **evenStream은** 0부터 시작해서 값이 2씩 계속 증가하는 무한스트림이다.

```jsx
Stream.iterate(0, n -> n+2);
```

- **generate() :** iterate() 처럼 무한스트림을 반환하지만, 이전결과를 이용해서 다음요소를 계산하지 않는다.

```jsx
Stream.generate(Math::random);
Stream.generate( () -> 1 );
```

여기서 주의해야할 점은 기본형 스트림 타입으로 참조변수를 다룰 수 없다는 점이다. 굳이 필요하다면 `mapToInt()` 같은 메서드로 변환해야 한다.

```jsx
IntStream streams =Stream.iterate(0 , n -> n+2 ).mapToInt(Integer::valueOf);

Stream<Integer> stream = streams.boxed();

```

### 빈 스트림

요소가 하나도 없는 빈 스트림을 생성 할 수 있다. 스트림에 연산을 수행한 결과가 하나도 없을 때, null 보다 빈 스트림을 반환하는 것이 낫다.

```jsx
Stream<Object> empty = Stream.empty();
empty.count(); // 0
```

### 두 스트림 연결

`concat()` 을 사용해서 두 스트림을 하나로 연결할 수 있다.

물론 스트림의 타입은 모두 같아야 한다.

```jsx
String[] str1 = {"123","456","789"};
String[] str2 = {"ABC","QWE","ZXC"};

Stream<String> str11 = Stream.of(str1);
Stream<String> str21 = Stream.of(str2);

Stream<String> concat = Stream.concat(str11, str21);
```

