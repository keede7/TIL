## Redis Operation Tip

`Redis`는 `Single Thread`로 동작을 한다. 한 사용자가 오래 걸리는 커맨드를 쓰면 나머지 모든요청은 수행 할 수 없고 대기를 하게 된다. 

이로 인해 장애도 빈번하게 발생한다. 

`keys`는 모든 `key`를 보여주는 커맨드인데 개발 할 때 자주 쓰다가 운영환경에서 실수로 손이 먼저나가는 경우가 있을 수 있다.

그래서 아예 그냥 사용하지 않는 것을 권장드린다. 이 커맨드는 `scan`으로 대체 할 수 있는데 이것을 쓰면 재귀적으로 `key`들을 호출 할 수 있다.

또한 `Hash`나 `Sorted Set`같은 자료구조는 내부에 여러 개의 아이템을 저장 할 수 있는데 `key` 내부에 `item`이 많아질수록 성능이 저하된다.

만약 좋은성능을 원한다면 하나의 `key`에 최대 100만개이상은 저장하지않도록 `key`를 적절하게 나누는 것이 좋다. 

`key`에 많은 데이터가 들어있을 때 `del`로 데이터를 지우면 그 `key`를 지우는 동안 아무런 동작을 할 수 없다. 

이 때 `unlink` 커맨드를 쓰면 `key`를 백그라운드로 지워주기 때문에 이것을 사용하는 것을 권장드린다.

---

**변경하면 장애를 막을수있는 기본설정값**

1) **STOP_WRITES_ON_BDSAVE_ERROR = NO**

- yes(default)
- `RDB` 파일 저장 실패시 `Redis`로의 모든 `write` 불가능

2) **MAXMEMORY-POLICY = ALLKEYS-LRU**

- `Redis`를 `Cache`로 사용 할 때 `Expire Time` 설정 권장
- 메모리가 가득 찼을 떄 `MAXMEMORY-POLICY` 정책에 의해 `key` 관리
    - **noeviction(default)** : 삭제 안함
    - **volatile-lru**
    - **allkeys-lru**

먼저 `STOP_WRITES_ON_BDSAVE_ERROR` 옵션은 기본으로 **YES**가 되어있는데 이 의미는 `RDB`파일이 정상적으로 저장되지 않았을 때 `Redis` 로 들어오는 모든 `write`를 차단하는 기능이다.

**만약 `Redis` 서버에 대한 모니터링을 적절히 하고있다면 이 기능을 꺼두는게 오히려 불필요한 장애를 막을 수 있는 방법이다.**

---


`MAXMEMORY-POLICY` 는 앞서 `Redis` 를 사용할 때 꼭 `Expire Time`을 설정하라고 말했는데 메모리가 한정되어 있기 때문에 이 값을 설정하지 않는다면 금새 `Redis`의 `Max-Memory`까지 데이터가 가득 차게되기 때문이다.  데이터가 가득찼을 때 `Max-Memory` 정책에 의해 데이터가 삭제된다.

기본값은 `noevication`인데 이건 메모리가 가득차면 더이상 새로운 `key`를 저장하지 않는다는 것을 의미한다.

즉 메모리가 가득차면 `Redis`에 새로운 데이터를 저장하는게 불가능하기 때문에 이는 장애로 발생할 수 있다.

---

`volatile-lru`는 가장 최근에 사용하지 않았던 `key`부터 삭제한다는 것을 의미한다. 

이 때 `expire`설정이 있는 `key`만 삭제한다는 것을 의미한다.

만약 메모리에 `expire`설정 값이 없는 `key`만 남아있다면 이 설정에서 위와 똑같이 장애상황이 발생할수있다.

그래서 추천설정은 `allkeys-lru`이다. 

이 값은 모든 키에 대해 `lru` 방식으로 `key`를 삭제하겠다는 것을 의미하고 이 설정에서는 적어도 데이터가 가득참으로 인해 장애가 발생할 가능성은 없다.


![Cache-Stampede](https://github.com/awse2050/TIL/blob/main/Redis/imgs/Cache-Steampede.PNG)

하지만 대규모 트래픽환경에서 TTL값을 너무적게 설정한 경우 Cache Stampede 현상이 발생할 가능성이 존재한다. 

1장에서 봣던 `look aside` 패턴에서는 `Redis`에 데이터가 없다는 응답을 받은 서버가 직접 `DB`로 데이터 요청을 한 뒤 이를 다시 `Redis`에 저장하는 과정을 거치는데,

`key`가 만료되는 순간 많은 서버에서 이 `key`를 같이 보고 있었다면 그 모든 애플리케이션 서버들이 `DB`에 가서 같은 데이터를 찾게되는 `Duplicate Read`가 발생한다. 

또 읽어온 값은 `Redis`에 각각 쓰게되는 `Duplicate Write`가 발생 할 수 있다.

이는 굉장히 비효율적인 상황이고 한번 이런상황이 발생하면 처리량도 느려지고 불필요한 작업들이 굉장히 늘어나기 때문에 장애로까지 이어질수있다. 

---

![Max-Memory-Config](https://github.com/awse2050/TIL/blob/main/Redis/imgs/Max-Memory-Config.PNG)

앞서 말한 것 처럼 `Redis`의 데이터를 파일로 저장할 때 `fork()`를 통해 자식 프로세스를 생성한다고 했었다.

그 자식 프로세스로 백그라운드에서 데이터를 파일로 저장하고 있지만 원래의 프로세스는 계속해서 일반적인 요청을 받아 처리하고있다. 

이게 가능한 이유는 `Copy on Write` 라는 기능으로 메모리를 복사해서 사용하기 때문인데 이로 인해 서버의 메모리 사용률이 2배로 증가하는 상황이 발생할 수 가 있다. 

만약 데이터를 영구저장 하지 않는다 하더라도 복제기능을 사용한다면 주의해야 한다. 

복제연결을 처음 시도하거나 혹은 연결이 끊겨 재시도를 할때 새로 `RDB`파일을 저장하는 과정을 거치기 때문이다. 

따라서 이런경우에는 `Max-Memory`값을 실제 메모리의 절반정도로 설정해주는것이 좋다. 

예상치못한 상황에 메모리가 풀이 차서 장애 발생할 가능성이 매우높기 때문이다.

----

![Memory-Management](https://github.com/awse2050/TIL/blob/main/Redis/imgs/Memory-Management.PNG)


`Redis`는 메모리를 사용하는 저장소이기 때문에 메모리 관리가 운영에서 제일중요하다. 모니터링을 할 때도 유의해야할 점이 있다. 

모니터링을 할때 `used_memeory` 값이 아닌 `used_memory_RSS` 값을 보는 것이 더 중요하다. 

`used_memory` 는 논리적으로 `Redis`에 얼만큼의 데이터가 저장되어 있는지를 나타내고 `RSS`는 `OS`가 실제로 `Redis`에 얼만큼의 메모리를 할당했는 지를 보여준다. 

따라서 실제 저장된 데이터는 적은데 `RSS`의 값은 큰 상황이 발생할 수 있고, 이 차이가 클 때 `framentation` 이 크다고 말한다.

주로 삭제되는 `key`가 많을 때 `framentation` 이 증가한다.

예를들어 특정시점에 `key`가 피크를 치고 다시 삭제되는 경우 또는 `TTL`로 인해 삭제가 과도하게 많을 경우에 발생한다.

위 그래프는 피크를 친 시점의 그래프인데 초록색 `used` 그래프는 확 내려간 반면에 노란색 `rss`그래프는 아직 많이 차있는것을 볼 수 있다. 

이 때 `activefrag` 라는 기능을 잠시 켜두면 도움이된다.

공식문서에서도 이 값을 항상 켜두는것보다는 단편화가 많이 발생 했을 때 켜두는 것을 권장하고 있다.


---

### 출처
[Redis 야무지게 사용하기](https://www.youtube.com/watch?app=desktop&v=92NizoBL4uA&ab_channel=NHNCloud)
