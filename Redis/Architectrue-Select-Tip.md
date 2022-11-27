## Redis Architecture Select Know-How

![Architecture-Criteria](https://github.com/awse2050/TIL/blob/main/Redis/imgs/Redis-Architectures.PNG)

`Replication` 구성, 즉 **복제 구성**은 `Master`와 `Replica` 이렇게만 존재하는 간단한 구조이다.

`Sentinal` 구성은 `Master`와 `Replica Node` 외에 추가로 `Sentinal Node`가 필요하다. 

`Sentinal`은 일반 노드들을 모니터링하는 역할을 한다.

`Cluster` 구성에는 최소 3대의 `Master`가 필요하며 **샤딩 기능**을 제공한다. 

---

1. **Replication**

단순히 복제만 연결된 상태를 말한다. 이 구조 뿐만 아니라 모든 `Redis`의 구조에서 복제는 비동기식으로 동작하는데 즉 `Master`에서 복제본에 데이터가 잘전달되었는지 매번 확인하고 기다리진 않는다. 
이 구조는 `HA`기능이 없기때문에 `Master`에 장애가 발생하면 수동으로 변경 해줘야 할 작업들이 많다. 

우선 `Replica Node`에 직접 접속해서 복제를 끊어야하고 어플리케이션에서도 연결 설정을 변경해서 배포하는 작업이 필요하다

2. **Sentical**

일반적인 다른 노드들을 계속 모니터링하는 역할을 한다. 그러다가 `Master`가 죽으면 자동으로 `Fail Over`를 발생시켜 기존의 `Replica Node`가 `Master`가 된다. 

이때 애플리케이션에서는 연결정보를 변경할 필요가 없다. 애플리케이션에서는 `Sentinal Node`만 알고있으면 되고 `Sentinal`이 변경된 `Master` 정보로 바로 연결시켜준다.

이 구조를 쓰려면 `Sentinal Process` 를 추가적으로 끊어야하고 `Sentinal`은 항상 3대 이상의 홀수로 존재해야한다.

NHN은 2대의 서버에는 일반 `Redis`와 `Sentinal`을 같이 띄우고 최저 사양의 다른 서버에는 `Sentinal Node`만 올려 쓰고있다.

3. **Cluster**

데이터가 여러 `Master Node`에 자동으로 분할되어 저장되는 **샤딩기능**을 제공한다.

이 구성에서 모든 노드가 서로 감시하고 있다가 `Master`가 비정상상태일 때 자동으로 `Fail Over`를 진행한다.

최소 3대이상의 `Master Node`가 필요하고 `Replica Node`를 하나씩 추가하는게 일반적인 구조이다.


---

![dd](https://github.com/awse2050/TIL/blob/main/Redis/imgs/Redis-Architecture-Criteria.PNG)


- **HA (High Availability)** 기능이 필요한가? 즉, 자동으로 `Fail Over`가 되어야할까?

이 질문이 대답이 맞다면 우리 서비스에 **샤딩기능**이 꼭 필요한지 확인한다. 

만약 서비스 확장을 위해 `Scale Out` 이 필요하면 `Cluster`구성을 사용하고 그게 아니라 `HA`만 필요할 것 같으면 `Sentinal`만 사용해도 충분하다.

만약 `HA`가 필요 없으며 복제기능은 필요한지 보고 필요하다면 `Replication`구조를 쓰면되고, 그것도 필요없다면 `Master` 하나를 띄운 `Stand-Alone` 구조를 사용하면 된다.
