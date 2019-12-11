# Spring WebFlux

## Define Reactive

reactive는 프로그래밍 모델을 뜻하는데, 변화에 반응하기 위해 만들어진 프로그래밍 모델 - 네트워크 컴포넌트는 I/O 이벤트에 반응하고, UI 컨트롤러는 마우스 이벤트에 반응한다.
non-blocking은 reactive이다.
왜냐하면, blocked되는 것 대신, 오퍼레이션의 완료 또는 데이터가 가능해졌다는 알림에 반응하는 것이다.

## Blocking IO

Socket은 서버-클라이언트 아키텍처에서 두 시스템 통신의 끝 지점이다. 참고로 java.net 패키지는 연결의 클라이언트 측과 서버 측을 각각 구현하는 클래스를 제공한다. 클라이언트가 서버에 요청을 하면 서버는 요청을 처리한 후 응답을 보내야 하는데, 이때 연결을 설정하기 위해 소켓이 사용된다. 애플리케이션은 소켓에 바인딩해야 하며, 서버는 클라이언트의 요청을 받기 위해서, 소켓을 수신 대기해야 한다. blocking IO 환경에서는 요청에 대한 응답을 처리할 때까지 Thread가 차단된다.

## Non-Blocking IO

Netty 임베비드 서버가 기본 스펙으로 추가되었다. Netty는 비동기 이벤트 기반의 고성능 네트워크 프레임워크 서버이다. Non-Blocking IO는 클라이언트의 요청 각각에 서버의 Thread를 바인딩하지 않는다. 대신, 개별 버퍼를 사용해서 요청에 대한 알림(Notification)을 주고 받는다. Non-Blocking IO는 여러 연결을 하나의 Thread로 처리할 수 있다.

## 참고
 * https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html
 * https://brunch.co.kr/@springboot/96
