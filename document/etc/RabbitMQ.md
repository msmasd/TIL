# RabbitMQ

* AMQP(Advanced Message Queueing Protocol) 메시지 브로커 소프트 웨어 오픈소스 입니다.
* AMQP는 표준 MQ 프로토콜

## 특징

* erlang, java
* Cluster, Federation
* HA
* publish/Subscribe
* 다양한 plugin 지원

## RabbitMQ Work Flow

* 메시지는 Message Queue를 통해 원하는 사용자에게 전달할 수 있습니다.
* Message Broker는 Producer와 Consumer 사이 중간자 역활을 담당합니다.
* Message는 Queue에 저장되어 소비자가 조회할때까지 저장하게 됩니다.

## 사용대상

* Message Queue는 빠른 응답을 원할 때 주로 사용됩니다.
  * 많은 리소스가 필요한 작업은 Event를 발생시켜 다른 API에게 위임한다.
  * 다른 API에게 위임함으로써 Request에 대해 빠르게 응답을 할 수 있습니다.
* Message를 많은 사람들에게 전달하고 싶을때 주로 사용됩니다.
* 두 Application간의 결합도는 Message Queue를 통해 낮출 수 있다.

## Exchanges

* Message는 바로 Queue로 가지않는다.
  * Message는 바로 Queue로 Publishing 하지 않습니다.
  * Producer는 Exchage에게 Message를 보냅니다.
* Exchange
  * 다른 Queue에게 Routing하는 역활을 담당합니다.
  * type
    * Direct Exchange
      * Message의 Routing key와 정확히 일치하는 Binding된 Queue로 Routing
    * Fanout Exchange
      * Binding된 모든 Queue에 Message를 Routing
    * Topic Exchange
      * 특정 Routing Pattern이 일치하는 Queue로 Routing
    * Headers Exchange
      * key-value로 정의된 Header 속성을 통한 Routing
  * configuration
    * Durability
      * 브로커가 재시작 될 때 남아있는지 여부
        * durable: 재시작해도 유지가능
        * transient: 재시작하면 사라짐
    * Auto-delete
      * 마지막 Queue 연결이 해제되면 삭제

## 용어
* Vhost(virtual host)
  * Virtual Host를 통해서 하나의 RabbitMQ 인스턴스 안에 사용하고 있는 Application을 분리할 수 있습니다.
* Connection
  * 물리적인 TCP Connection, HTTPS -> TLS(SSL) Connection을 사용
* Channel
  * 하나의 물리적인 Connection 내에 생성되는 가상의 Connection
  * Consumer의 process나 thread는 각자 Channel을 통해 Queue에 연결될 수 있습니다.

## Spring-boot 연동

### 1. docker-compose.yml

## 참고
* https://nesoy.github.io/articles/2019-02/RabbitMQ
