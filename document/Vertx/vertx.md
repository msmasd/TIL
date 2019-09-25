# Vertx

## 1. 정의

Vert.x는 NodeJS와 같은 비동기 소켓서버 프레임워크입니다.

기존 Tomcat의 경우에는 HTTP request가 들어오면, request가 앞에 request Queue에 쌓이게 된다.
쌓이게된 request들은 Thread Pool에 있는 Thread에게 하나씩 할당되어, request를 처리하고 작업이 끝나면 request가 들어온 connection으로 response를 보낸후 작업을 끝낸다. 작업이 끝난 Thread는 다시 Tread Pool로 들어간다.
이런 구조에서, Tomcat이 순간적으로 동시에 처리할 수 있는 Connection의 수는 Thread Pool의 Thread 수만큼이 되는데, 일반적으로 Tomcat은 50~500개의 Thread정도가 적절하다.
즉, Tomcat 서버 하나는 동시에 최대 500여개의 Connection을 처리할 수 있다고 보면 된다.

하지만 요즘 요구사항은 동시에 수십만, 수백만개의 Connection을 처리해야 하는 요구사항이 생기고 있다. 또한, HTTP 뿐만 아니라, TCP와 같은 request를 처리해야 하는 여러가지 Protocol지원 문제도 있고, 단순히 request/response기반의 HTTP 요청 뿐만 아니라, HTTP long polling/Streaming과 같은 push성 서비스를 구현하려면, 동시에 유지되어야 하는 Connection수가 많아야 한다.(모든 클라이언트가 Connection을 물고 있기 때문에)
이러한 요구사항을 반영하기 위해서 나온 서버들이 nodejs와 같은 비동기 소켓 서버이다.

Vertx는 nodejs의 자바 버전정도로 보면 되는데, 구조부터 기능까지 nodejs에 비해서 발전된 모습이 매우 많다.
vertx는 nodejs와 마찬가지로 single thread model이다. WAS와 같이 Thread pool을 이용하는 것이 아니라, 하나의 Thread로 모든 작업을 처리한다. Single Thread 모델을 사용할 경우 Thread의 Context Switching 오버해드를 줄일 수 있어서 성능에 도움이 되고, 또한 Multi threading에서 고민해야 하는 locking처리나 공유 데이터 처리에 대해서 전혀 걱정할 필요가 없다.

Vertx는 Single Thread로 도는데, 이를 Event Loop(EL)이라고 한다. Vert.x에 일단 클라이언트가 연결되면, Event Loop가 각 연결된 개별 socket들에 대해 event를 검사한후에, event가 있으면 그 function을 수행해준다.

모든 연결된 소켓에 대해서 Event loop가 도는데, 보통 하나의 Event loop를 처리하는 Thread에서 10,000 ~ 20,000개 정도의 Connection을 처리하는데는 큰 무리가 없다.

## 2. 개념

### 2.1 Vert.x instance

Vert.x instance는 하나의 Vert.x 서버 프로세스로 보면 된다. 하나의 Vert.x JVM 프로세스를 하나의 Vert.x instance라고 이해하면 된다.

### 2.2 일반 Verticle (aka standard verticle, ELP verticle)

먼저 Verticle이다. Verticle은 Vert.x에서 수행되는 하나의 프로그램을 이야기 한다. 앞서 설명했듯이, 자바의 Servlet과 같은 개념으로 이해하면 된다.

### 2.3 Verticle instance

Verticle 코드가 로딩되서 객체화 되면, 이를 Verticle instance(하나의 Verticle Object)라고 한다.
Verticle은 ELP안에서 수행이 되며, 항상 같은 쓰레드에서 수행이 된다. Verticle instance는 절대 multi thread로 동작하지 않고, **single thread에서만 동작한다**

하나의 Vert.x instance는 하나의 JVM위에서 동작한다.
Verticle Code는 각 Verticle instance당 할당된 Class Loader에 의해서 로딩되고, 객체화가 된다. ELP Thread가 하나인 경우인데, 이 경우, Verticle Instance들은 Queue에서 기다리게 되고, 순차적으로 ELP Thread를 점유하면서 순차적으로 실행된다. 단 수행되는 순간에는 Single Thread 모델이기 때문에, 하나의 Verticle만 순산에는 수행된다(즉, Verticle이 100개이고, ELP가 1개라도 모든 100개의 Verticle은 ELP 한개에서 순차적으로 수행된다)
Vert.x에서는 CPU Core수에 따라서 자동으로 ELP Thread를 생성해준다.
ELP Thread수를 지정하려면 vertx.pool.eventloop.size를 System Property로 주면 된다. ELP Thread의 수는 CPU수보다 작아야 Thread switching이 발생하지 않아서 조금 더 높은 성능을 낼 수 있다.

### 2.4 Worker Verticle

일반 Verticle은 개별 socket에 대해서 ELP를 돌면서 Event 처리를 한다. 그래서 해당 Event 처리에 오랜시간이 걸리면 전체적으로 많은 성능이 떨어지기 때문에 문제가 된다.
예를 들어 하나의 event 처리에 100ms가 걸리는 작업이 있다면, 연결된 Connection이 100개가 있을 경우 전체 socket에 대해서 한번 이벤트를 처리하는데 10초가 걸리고, 처리가된 소켓이 다음 이벤트를 받을 수 있을 때까지 10초가 걸린다. 즉 클라이언트 입장에서 연속적으로 요청을 받았을때, 두번째 요청에 대해서 응답을 받는 것은 10초후라는 이야기가 된다. 이는 Single Thread모델이기 때문에 발생하는 문제인데, Vert.x에서는 이런 문제를 해결하기 위해서 Wokker Verticle이라는 형태의 Verticle을 제공한다.

이 Worker Verticle은 쉽게 생각하면, Message Queue를 Listen하는 message subscriber라고 생각하면 된다. Vert.x 내부에 event bus를 이용해서 메시지를 보내면, 뒷단의 Workder Verticle이 message Queue에서 메시지를 받아서 처리를 한 후에 그 결과 값을 다시 event bus를 통해서 caller에게 보내는 형태이다.(Asynchronous call back 패턴)
event bus로 request를 보낸 Verticle은 response를 기다리지않고, 바로 다음 로직을 진행하다가, Workder Verticle에서 작업이 끝난 이벤트 메시지가 오면 다음 ELP가 돌때 그 이벤트를 받아서 응답 메시지를 처리를 한다.
DB작업이나, 시간이 오래 걸리는 작업은 이렇게 Worker Verticle을 이용해서 구현할 수 있다.

### 2.5 Worker Verticle instance & Thread pooling

Worker Verticle은 Thread Pool을 지원한다는 것이다. ELP Verticle과 마찬가지로, Worker Verticle instance는 독립된 클래스 로드로 로딩되어 생성된 객체로 쓰레드에서 각각 다른 독립된 instance가 수행되기 때문에, Thread safe하다.
단, Worker Thread의 경우 공용 Thread Pool에서 수행된다. ELP Thread의 경우 Verticle은 항상 지정된 Thread에서 수행되는데 반해, Worker Verticle Instance는 Thread Pool의 아무 쓰레드나 유유 쓰레드에서 수행된다.

### 2.6 Event Bus

Event Bus는 일종의 Message Queue와 같은 개념으로, Verticle간에 통신이나, Vert.x Instance간의 통신이 가능하게 한다. Verticle간의 통신이랑 Java로 만든 Verticle에서 Python으로 만든 Verticle로 메시지 전달이 가능하다. 또한 Event Bus를 이용하면 서로 분리되어 있는(다른 JVM 프로세스로 기동되는 또는 서로 다른 하드웨어에서 기동되고 있는) Verticle간에 메시지를 주고 받을 수 있다.
이 메시지 통신은 1:1통신 뿐만 아니라, 1:N(Publish & Subscribe) 형태의 통신까지 함께 지원한다.
pattern을 사용하는데 반해서, Event Bus는 call back pattern을 사용한다.
즉, JMS나 RabbitMQ의 일반적인 경우 클라이언트가 메시지 큐에 메시지를 넣고 바로 리턴하는데 반해서, Event Bus를 이용하면 클라이언트가 메시지를 넣고 바로 리턴이 된 다음, 큐를 사용하는 Consumer가 메시지를 처리한 후에, 메시지를 보낸 클라이언트에게 다시 메시지를 보내서, 처리를 하도록 할 수 있다.
예를 들어 하나의 HTTP request가 들어왔을 때, event bus에 메시지를 넣고 해당 클라이언트는 내부적으로 리턴이 된다.(HTTP response는 보내지 않고, Connection은 물고 있다.) 그 후에 Worker Verticle에서 작업을 처리한 후 Call back reply를 보내면, 클라이언트에서 이 이벤트를 받아서 HTTP connection으로 response를 보낼 수 있다.
이 구조가 있기 때문에, Single Thread모델임에도 불구하고 뒷단에서 비동기 메시지를 처리하는 방식을 이용하여 long transaction도 핸들링할 수 있게 된다.

### 2.7 Shared data 처리

### 2.8 Module

모듈은 하나의 Runnable Application으로, 자바의 일종의 WAR파일이라고 생각하면된다.
mod.json(WAR파일의 web.xml과 같은 메타 정보 description)에 메타 정보를 정의한 후에, 클래스와 jar파일등의 라이브러리, 그리고 기타 애플리케이션에서 사용할 파일들을 같이 묶어서 패키징한 형태이다.
이 Module은 Maven repository 시스템등에 저장 및 배포될 수 있다(vagrant나 docker 컨셉과 비슷한듯. 이제 Runtime Application도 repository를 사용하는 추세인거 같다.)

### 2.9 Pumping

네트워크 Flow Control을 해주는 기능으로, 읽는게 쓰는거 보다 빠르면(예를 들어 socket에서 읽고, 파일에 쓰는) Queuing이 많이 되고, 메모리 소모가 많아져서 exhausted된다. 이를 막기 위해서 쓰다가 Q가 차면 socket read를 멈추고 있다가 write q가 여유가 생기면 다시 읽는 것과 같은 flow control이 필요한데, 이를 pump라고 한다.

### 2.10 HA(High Availibility)

Vert.x는 HA개념을 지원한다. Vert.x에서 HA란, HA mode로 Vert.x instance를 구성했을때, 해당 Vert.x instance가 비정상종료(kill등)되면, 해당 instance에서 돌고 있던 Vert.x Module을 다른 Vert.x Instance로 자동으로 옮겨서 실행해주는 기능이다.
실행시 -ha 옵션을 줘서 실행하는데, 하나의 클러스터 내에서도 hagroup을 지정하여 그룹핑이 가능하다. 예를 들어 클러스터에 10개의 노드가 있을때, 4개는 업무 A용 HA그룹으로 나누고, 6개는 업무 B용으로 나누는 식으로 업무별로 나눌 수도 있고
Machine이 3개가 있고, 2개의 업무를 수행하는데, 각 Machine마다 Vert.x instance를 2개씩 돌린다고 했을때,
Machine 1: vertx.instance=hagroup_A, vertx.instance2=hagroup_B
Machine 2: vertx.instance=hagroup_A, vertx.instance2=hagroup_B
Machine 3: vertx.instance=hagroup_A, vertx.instance2=hagroup_B
이런식으로 구성을 하게되면, Machine단위로 HA가 넘어가도록 구성을 할 수 있다.

### 2.11 Clustering

HA없이 클러스터링 구성만을 할 수 있는데, event_bus로 메시지를 주고 받을려면, 해당 vert.x instance들이 같은 cluster내에 들어 있어야 한다.

## 참조

1. [조대협의 블로그 vertx](https://bcho.tistory.com/870)
