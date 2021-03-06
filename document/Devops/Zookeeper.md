Zookeeper를 활용한 Redis cluster 관리

Redis
* 샤딩을 지원하지 않는다.
    * 확장성을 확보하기 위해서는 수동으로 샤드를 구성한 후 애플리케이션 서버에서 해싱 로직에 의해 샤딩을 지원해야 하고, 클러스터 관리 도구도 직접 만들어야 한다.
* 클러스터 구성에서 중요한 Failover기능이 없다. Master-Slave Replication 구성 시 Master가 장애가 날 때 자동으로 처리하지 않아 일일이 수동으로 복구해야 한다.

## 샤딩?

같은 테이블 스키마를 가진 데이터를 다수의 데이터베이스에 분산하여 저장하는 방법.
Horizontal Partitioning

## Zookeeper

분산 처리 환경에서 사용 가능한 데이터 저장소.
예를 들면 분산 서버 간의 정보 공유, 서버 투입/제거 시 이벤트 처시, 서버 모니터링, 시스템 관리, 분산 락 처리, 장애 상황 판단 등 다양한 분야에서 활용할 수 있다.

Zookeeper는 데이터를 디렉터리 구조로 저장하고, 데이터가 변경되면 클라이언트에게 어떤 노드가 변경됐는지 콜백을 통해서 알려준다. 데이터가 저장할 때 해당 세션이 유효한 동안 데이터가 저장되는 Ephemeral Node라는 것이 존재하고, 데이터를 저장하는 순서에 따라 자동으로 일렬번호가 붙는 Sequence Node라는 것도 존재한다.

### 데이터 모델

ZooKeeper는 디렉터리 구조로 데이터를 저장한다. 특징을 살펴보면 Persistent를 유지하기 위해서 트랜잭션 로그와 스냅샷 파일이 디스크에 저장되어 시스템을 재시작해도 데이터가 유지된다. 각각의 디렉터리 노드를 znode라고 명명하며, 한 번 설정한 이름은 변경할 수 없고 스페이스를 포함할 수도 없다.

1. Persistene Node: 한 번 저장되고 나면 세션이 종료되어도 삭제되지 않고 유지되는 노드
2. Ephemeral Node: 특정 노드를 생성한 세션이 유효할 동안 그 노드의 데이터가 유효한 노드. Zookeeper Server에 접속한 클라이언트가 특정 노드를 Ephermeral Node로 생성했다면 그 클라이언트와 서버간의 Ping알 제대로 처리하지 못하면 해당 노드는 자동으로 삭제된다. (**이 노드를 통해 클라이언트가 동작하는디 여부를 쉽게 판단**)
3. Sequence Node: 노드 생성 시 sequence number가 자동으로 붙는 노드. 이 기능을 활용해 분산 락 등을 구현할 수 있다.

## Zookeeper 특징

ZooKeeper 서버는 Leader와 Follower로 구성되어 있다. 서버들끼리 자동으로 Leader를 선정하며 모든 데이터 저장을 주도한다.

모든 서버는 동일한 데이터를 각각 가지고 있고, 클라이언트가 어떤 서버에 연결되어 데이터를 가져가더라도 동일한 데이터를 가져가게 된다.

추가내용은 아키텍처적인 내용이라 일단 패스..


참고 https://d2.naver.com/helloworld/294797
