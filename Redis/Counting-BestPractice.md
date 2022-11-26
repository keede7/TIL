### Redis에서 Counting 하기 적절한 데이터 타입.

`Key`를 하나 만들어서 `Counting`할 상황마다 하나씩 증가 시키는 것이다. 

1) `String`

- 단순 증감연산
- `INCR` , `INCRBY`, `INCRBYFLOAT`, `HINCRBY` , `HINCRBYFLOAT`, `ZINCRBY`

```jsx
set score:a 10

// 1개씩 증가하는 방식.
INCR score:a // 11

// 직접 지정
INCRBY score:a 4 // 15
```

2) `Bits`

- 데이터 저장 공간 절약
- 정수로 된 데이터만 카운팅 가능

우리 서비스에 오늘 접속한 유저 수를 세고싶으면 날짜 `key`를 만들어서 유저 `ID`에 해당하는 `bit`를 `1`로 올려주는 것이다.

한개의 `bit` 이 한명을 의미하므로 천만 명의 유저는 천만 개의 `bit`으로 표현이 가능하며, 이것은 `1.2MB` 정도밖에 차지하지 않는다.

```jsx
SETBIT visitors:20210817 3 1 // 0

SETBIT visitors:20210817 6 1 // 0

// 1로 설정된 값을 모두 카운팅
BITCOUNT visitors:20210817 // 2
```

이 방법을 쓰려면 모든 데이터를 정수로 표현할 수 있어야 한다.

즉 `userId` 같은 값이 `0` 이상의 정수값일 때에만 카운팅이 가능하며 그런 `Sequential`한 값이 없으면 이 방법을 쓸 수 없다.

3) `HyperLogLogs`

모든 String 데이터값을 유니크하게 구분할 수 있다.

- `set`과 비슷하지만  대용량 데이터를 카운팅 할 때 적절(오차 0.81%)
- 저장되는 데이터 개수에 상관없이 모든 값이 `12KB`로 고정되어 저장되며 몇 천만 건이 들어오던 `12KB`
- 저장되는 데이터는 다시 확인이 불가능하다.

**경우에 따라서 데이터를 보호하기 위한 목적으로도 적절하게 사용할 수 있다.**

```jsx
PEADD crawled:20211024 "http://www.google.com" // 1

PEADD crawled:20211024 "http://www.naver.com" // 1

---수백만건 저장 ----
PECOUNT crawled:20211024 // 783478

PEMERGE crawled:all crawled:20211023 crawled:20211022
```

**3-1) 사용예시**

1. 우리 웹사이트에 들어온 `IP`가 몇 개가되는지,
2. 하루종일 크롤링한 URL개수가 몇 개인지,
3. 우리 검색엔진에서 검색된 단어가 몇 개가 되는지, 
4. 엄청 크고 유니크한값을 계산할때 아주 적절하다.

`PFADD` 커맨드로 데이터를 저장하고 `PFCount` 커맨드로 `Unique`하게 저장된 값을 조회 할 수 있다. 

만약, 일별 데이터로 저장을 했는데 일주일치 를 취합해서 보고싶다면 `PFMERGE` 커맨드로 `key`를 `Merge`해서 확인할 수 있다.


----


### 메세징

1) `List`

`Redis`의 `List`는 `Message Queue`로 사용하기 적절하다.

특히 자체적으로 `blocking` 기능을 제공하기 때문에, 이를 적절히 사용하면 불필요한 `polling`을 막을 수 있다. 

`key`가 있을 때만 데이터를 저장할 수 있는 `LPUSHX`, `RPUSHX` 가 있다. 잘 사용하면 유용한 기능이다.

`key`가 이미 있다는건 이미 사용했던 `key`라는 것이고 사용했던 `Queue`에만 `Message`를 넣어 줄 수  있어 비효율적인 데이터 이동을 막을수있다. 

2) `Streams`

이것은 `Log`를 저장하기 가장 적절한 자료구조이다.

실제 서버에 로그가 쌓이는것처럼 모든 데이터는 `append-only` 방식으로 저장되며 중간에 데이터가 바뀌지않는다.

```jsx
XADD mysteam * sensor-id 1234 temperature 19.8
"1634823241307-0"

XADD mysteam * sensor-id 1234 temperature 20.4
"1634823253756-0"
```

`XADD`를 이용해서 `mystream`이라는 `key`에 데이터를 저장하고 있다

`key` 이름 옆에 `*`은 `ID`이며 `ID`를 직접 입력할 수 있으나 일반적으로는 `*`로 입력하면 `Redis`가 알아서 저장하고 `ID`값을 반환해준다.

이때 반환되는 `ID`값은 데이터가 저장된 시간을 의미한다.

이 값 뒤로는 `Hash`처럼 `key-valu` 로 저장되는데 예제에서는 `sensor-id` 값에 `1234`를 `온도`에는 `19.8`을 저장한것을 의미한다.

`Streams` 의 데이터를 읽어오는 방법은 다양한데 `ID`값을 이용해 시간 대역대로 저장된 값을 검색할 수도 있고 실제 서버에서 로그를 읽을때 사용하는 `tail -f` 처럼 새로 들어오는 데이터만 `Listening`할수도있다.

`Kafka`처럼 `Cosumer Group`이라는 개념이 존재하므로 원하는 `Consumer`만 특정 데이터를 읽게 할 수도있다. `Streams`에서는 `Kafka`의 개념을 많이 차용했는데, 공식문서에서는 `Streams`는 `Messaging Broker`가 필요할때 `**Kafka`를 대체해서 사용 할 수 있는 자료구조라고 소개하고 있다.**
