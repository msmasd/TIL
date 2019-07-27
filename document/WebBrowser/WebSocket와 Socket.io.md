WebSocket와 Socket.io

웹소캣이 있기까지

전형적인 브라우저 렌더링 방식은 http요청에 대한 http응답을 받아서 브라우저를 깨끗이 지우고받은 내용을 새로 표시하는 방식이다. 이 과정은 브라우저가 깜빡여진다.
이러한 깜빡임없이 해당 부분만 다시 그리며 실시간으로 사용자와 상호작용하는 방식이 나타나고 이런 방식을 선호하는 사용자가 많아지면서 RIA(Rich Internet Application)이 발전함

이런 방식을 위해서 hidden frame, long pulling, stream등 다양한 방법을 해왔다.
이 방법들도 http요청에 의한 응답을 받는 단방향 메시지 교환 ‘규칙’을 변경하지 않고 구현한 것이다. 그래서 상호작용하는 것을 구현하기에 복잡한 코드로 구현되어진다.

보다 쉽게 상호작용하는 웹 페이지를 만들려면 브라우저와 웹서버 사이에 더 자유로운 양방향 메시지 송수신이 필요하다. 그래서 HTML5 표준안의 일부로 WebSocket API(이후 WebSocket)이 등장했다.

WebSocket은 기존의 요청-응답 관계 방식보다 더 쉽게 데이터를 교환할 수 있다.

WebSocket 프로토콜
WebSocket은 다른 HTTP와 동일하게 80포트를 통해 웹 서버에 연결한다.
HTTP 프로토콜 버전은 1.1이지만 upgrade 헤더를 사용하여 웹 서버에 요청한다.

브라우저는 Upgrade: WebSocet 해더 등과 함께 랜덤하게 생성한 키를 서버에 보낸다. 웹 서버는 이 키를 바탕으로 토큰을 생성한 후 브라우저에 돌려준다. 이런 과정으로 WebSocket 핸드쉐이킹이 이루어진다.
그 뒤 Protocol Overhead 방식으로 웹 서버와 브라우저가 데이터를 주고 받는다.
Protocol Overhead 방식은 여러 TCP 커넥션을 생성하지 않고 하나의 80번 포트 TCP 커넥션을 이용하고, 별도의 헤더 등으로 논리적인 데이터 흐름 단위를 이용하여 여러 개의 커넥션을 맺는 효과를 내는 방식.
이런 방식을 사용하고 있기 때문에 방화벽이 있는 환경이여도 사용 가능하다.

웹서버중에는 Apache에서 별도 모듈을 설치하여 WebSocket을 사용할 수 있다. JEE 환경의 WAS에서는 Jetty, Node.js에서도 Websocket을 사용할 수 있다.

웹소켓에서는 ws://로 프로토콜을 사용한다.
암호화는 wss://이다.

Socket.io는 JavaScript를 이용하여 브라우저 종류에 상관없이 실시간 웹을 구현할 수 있도록 한 기술이다.
WebSocket, FlashSocket, AJAX Long Polling, AJAX Multi part Streaming, IFrame, JSONP Polling을 하나의 API로 추상화한 것이다. 즉 브라우저와 웹 서버의 종류와 버전을 파악하여 가장 적합한 기술을 선택하여 사용하는 방식이다. 

참고내용 https://d2.naver.com/helloworld/1336
