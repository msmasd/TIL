# EventBus

Vert.x에서 제공하는 eventBus입니다. eventBus는 각 서버 및 클라이언트단끼리도 통신을 할 수 있게 만들수 있게 해줍니다.

eventBus는 다른 언어로 쓰여지거나 클라이언트와도 통신이 가능합니다.

## 1. 구성요소

### 1.1 Handlers

핸들러로 메시지를 받고, 핸들러를 address에 register하여 작업한다.
서로 다른 많은 핸들러를 하나의 address에 등록할 수 있고, 하나의 핸들러를 여러 다른 주소에 등록 할 수 있다.

## 1.2 message 종류

1. publish/subscribe
2. Point-to-Point
3. Request-Response

### 1.2.1 publish/subscribe

모든 주소에 등록되어있는 핸들러들에게 메시지를 보내는 방식이다.

### 1.2.2 Point-to-Point

주소에다가 메시지를 보내고, Vert.x는 해당 주소에 있는 핸들러 중 한개의 핸들러에게만 보낸다.(핸들러를 정하는 알고리즘은 non-strict round-robin)

(option)답장하는 핸들러를 메시지 보낼때 명시할 수 있다.

### 1.2.3 Request-Response

답장을 하고 답장을 받은 핸들러는 또다시 답장을 할 수 있는데 이것이 Request-Response 패턴이다.

## 1.3. message type

메시지 타입은 primitive/simple type, String or buffers, JSON(흔한 관례) 그리고 codec을 사용하여서 보낼 수 도 있다.

## 2. EventBus API

### 2.1 Getting the event bus

```java
EventBus eb = vertx.eventBus();
```

### 2.2 Registering Handlers

```java
EventBus eb = vertx.eventBus();

// using comsumer
eb.consumer("news.uk.sport", message -> {
  System.out.println("I have received a message: " + message.body());
});
```

이벤트 버스 객체를 만들고
consumer로 특정 address에 응답하는 핸들러를 만들었다.

### 2.3 Publishing or sending message

```java
// publish
eventBus.publish("news.uk.sport", "Yay! Someone kicked a ball");
// send
eventBus.send("news.uk.sport", "Yay! Someone kicked a ball");
```

### 2.4 receiver or sender message

```java
// receiver
MessageConsumer<String> consumer = eventBus.consumer("news.uk.sport");
consumer.handler(message -> {
  System.out.println("I have received a message: " + message.body());
  message.reply("how interesting!");
});

// sender
eventBus.send("news.uk.sport", "Yay! Someone kicked a ball across a patch of grass", ar -> {
  if (ar.succeeded()) {
    System.out.println("Received reply: " + ar.result().body());
  }
});
```

