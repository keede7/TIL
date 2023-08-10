## Critical-Section ( `임계 구역` )

여러 `프로세스`간의 동기화 문제는 `임계 구역` 부터 시작한다.

즉, **동시성 이슈**에 대한 문제의 가장 기본이되는 개념이라고 볼 수 있다.

---

### 임계 구역

임계 구역은 각 프로세스 마다 소유를 하고 있으며, 해당 구역에서는 공유자원을 가지고 활동을 하는 공간이라고 볼 수 있다.

이 구역에서는 다른 프로세스가 들어와서 (정확히는 `진입 허가`를 받아야 한다.) 공유된 자원을 사용 할 수 있습니다.

결국엔, 본인 뿐만 아니라 다른 프로세스도 자신의 임계 구역에서 리소스에 대한 활동을 할 수 있다는 것입니다.

그리고, 다른 프로세스는 `진입 허가`를 받아야 하기 때문에, 임계 구역에서 발생한 활동들을 동기화하는 프로토콜을 설계 해줘야 한다.

이 `진입 허가`를 구현하는 부분을 **진입 구역**이라고 한다.

---

여기서 문제는, 다른 프로세스가 활동을 할 수 있지만, 둘 이상의 프로세스가 동시에 해당 구역에서 활동을 할 수 가 없습니다.

왜냐하면 공유 자원을 사용하는 것이기 때문에, 공유 자원에 대한 작업결과가 둘 이상의 프로세스에 의해서 동기화가 되지 않는다면 큰 문제가 발생하기 때문이다.

동시에 둘 이상의 프로세스가 접근하려 한다면 **경쟁 조건**(`Race Condition`)이 발생하기 때문에 동시성 이슈를 해결할 수 있는 방안을 마련해야 한다.





