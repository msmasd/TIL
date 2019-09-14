# Bean Scope

스코프

* 싱글톤(default)
* 프로토타입
  * Request
  * Session
  * WebSocket
  * ...

```java
@Component @Scope("prototype") // 그냥 스트링으로만 되어있어서 타입세이프하지않음.
public class Proto {}
```

프로토타입 빈이 싱글톤 빈을 참조하면?

* 아무 문제 없음.

싱글톤 빈이 프로토타입 빈을 참조하면?

* 프로토타입 빈이 업데이트가 안되네? -> 싱글톤 빈은 딱 한번만 만들어지기때문에 proto 빈이 계속 동일한 object이다.
* 업데이트 하려면 -> proxyMode를 사용(이해는 어렵)
  * scoped-proxy
  * Object-Provider (개인적으로 좋아하지않는다. 왜냐? 코드에 스프링코드가 들어가서.)
  * Provider (표준)

proxyMode를 사용한다는것은 이 빈을 프록시로 감싸라 라는 것
Single이 직접 proto를 직접 접근하면 매번 바꿔줄수가 없어서 proxy로 감싸는 proto를 참조해야 한다.
실제적으로 주입이 되는 빈은 proxy를 가지고있지만 타입은 proto이다.

프록시 (https://en.wikipedia.org/wiki/Proxy_pattern)
싱글톤 객체 사용시 주의할 점

* 프로퍼티가 공유.
* ApplicationContext 초기 구동시 인스턴스 생성.
* ThreadSafe한 방식으로 코딩을 해야한다.
* 애플리케이션컨텍스트 만들때 만들어지기때문에 구동 속도가 조금 느릴수 있다.
