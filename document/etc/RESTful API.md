# REST API

* REST는 Representational state transfer의 약어
* REST는 요소로는 크게 리소스, 메소드, 메세지 3가지 요소로 구성
  * Ex. 이름이 Terry인 사용자를 생성
    * "사용자"는 생성되는 리소스 (http://myweb/users)
    * "생성한다"라는 행위는 메서드 (HTTP POST)
    * "이름이 Terry인 사용자"는 메시지 ( { "users": {"name":"Terry"}})

## HTTP에서의 representation

GET 메서드의 정의는 다음과 같다

> The GET method requests transfer of a current selected representation for the target resource.

즉, target resource에 대한 현재의 선택된 representation 하나를 반환한다.

위 정의에서 "target resource"란 https://example.org/greeting 라는 uri가 가리키는 리소스이다.
리소스는 HTTP 요청의 대상이다.
HTTP는 리소스의 개념을 제한하지 않으며 무엇이든 될 수 있다. 여기서는 "hello"라는 텍트스 자체가 리소스가 아니라, "환영의 의미를 담은 문서"가 리소스가 된다고 볼 수 있을 것이다.

그럼 "현재의 선택된 representation"이란?
* 일단 representation은 어떤 리소스의 특정 시점의 상태를 반영하고 있는 정보
* 하나의 representation은 representation data와 representation metadata로 구성된다.
* 위의 예에서, "hello"가 representation data이고, "Content-Type: text/plain"과 "Content-Language: en"이 representation metadata이다. (HTTP 해더들 중 representation metadata에 해당하는 것이 있고 아닌것도 있다.)

* 현재는?
  * 말 그대로 해석하면 된다.
  * 만약 https://example.org/greeting이 가리키는 리소스의 representation이 "hi"에서 "hello"로 수정되었다면 "현재" representation은 "hi"가 아닌 "hello"가 될 것 이다.

* "선택된"은?
  * 하나의 리소스의 현재 representation이 하나 이상이 될 수 있으며, 그 중 하나가 선택되었음을 의미한다.
  * 즉, "greeting" 리소스의 현재 representation은, 영어 사용자를 위한 "hello", 한국어 사용자를 위한 "안녕하세요" 등 여러가지가 될 수 있었는데, 이들 중 하나가 선택되었다는 의미이다.
  * metadata를 포함하여 representation들의 예를 들어보자
    * 영어 사용자를 위한 representation
      * Content-Type: text/plain
      * Content-Language: en
      * hello
    * 한국어 사용자를 위한 representation
      * Content-Type: text/plain
      * Content-Language: kr
      * 안녕하세요
  * 그렇다면 이들 중 하나를 "선택"하는 것은 누가 어떻게 하는가?
    * 클라이언트와 서버간의 내용 협상(Content negotiation)을 통해 선택하며, 선택의 주체는 협상 방법에 따라 다르다.
    * 사전 협상(proactive negotiation)의 경우, 서버가 선택한다.
      * 클라이언트가 GET 요청에 "Accept-Language: ko"해더를 포함시켜 한국어를 선호함을 밝혔다면
      * 서버는 가장 적절한 representation인 "안녕하세요"로 응답할 것이며
      * 만약 여기에 "Accep: text/html; charset=UTF-8" 해더도 포함시켜서 한국어로 되어있고, UTF-8로 인코딩된 HTML 문서를 선호한다고 밝혔다면
      * "<html><body>안녕하세요</body></html>" representation을 선택하여 응답할 것이다.
      * 만약 적절한 representation이 존재하지 않는다면 406 Not Acceptable로 응답할 것이다.

## REST에서의 representation

REST는 REpresentation State Transfer의 줄임말이다.
"State"는 웹 애플리케이션의 상태를 의미하며,
"Transfer"는 이 상태의 전송을 의미한다.

웹 브라우저로 웹사이트를 이용하는 예를 들어보면
* 웹 페이지 A를 보고있던 사용자가 웹 페이지 B로 가는 링크를 클릭하면, 웹 브라우저는 링크가 가리키는 웹 페이지 B를 렌더링해서 보여줄 것이다.
* 위의 상황에서 웹 애플리케이션은 무엇인가?
  * 웹 브라우저와 웹 서버가 연결되어 사용자에게 가치를 제공하는 애플리케이션이다.
  * 웹 서버가 웹 애플리케이션인 것이 아니라, 웹 브라우저가 웹 서버에 접속해야 웹 에플리케이션이다. 접속이 되어야 웹 애플리케이션이다!!
  * 두명의 사용자가 각각 자신의 웹 브라우저로 같은 웹 서버에 접속한다면, 두개의 웹 애플리케이션이 실행되고 있는 것이다.
* 링크를 클릭함으로써 브라우저가 보여주던 페이지는 A에서 B로 바뀌었다.
* 즉, 웹 애플리케이션의 상태가 변경된 것이다. 또한 이 상태의 변경은 representation의 전송(transfer)을 통해 이루어졌다.
* 그렇기 때문에 이것이 REpresentation State Transfer이다.

여기서 두가지 주의할 점이 있다.

1. Transfer는 상태의 전이(transit)을 의미하는 것이 아니다.
   1. 사용자가 링크를 클릭함으로써 웹 애플리케이션의 상태가 전이된 것은 사실이지만, Transfer가 의미하는 것은 그 전이가 아니라 network component 사이에서의 전송을 말한다.
   2. 즉, 이 예에서는 서버에서 클라이언트로의 웹 페이지 전송을 의미하는 것이다.
2. 리소스의 상태와 애플리케이션의 상태는, 둘 다 동일하게 "state"라는 단어로 표현되고 있긴 하지만, 본질적으로 완전히 다른것이다.
   1. 앞서 representation이란 "어떤 리소스의 특정 시점의 상태(state)를 반영하고 있는 정보"라고 말했다.
   2. 그것은 리소스의 상태지 애플리케이션의 상태는 아니다.
   3. 애플리케이션의 상태란, 웹 애플리케이션이 웹 페이지 A를 렌더링하다가 B를 렌더링하는 것으로 바뀐 그 상태를 말하는 것이다.

## 모든 payload는 representation

HTTP 메시지는 payload로 전달되는 모든 것은 하나의 representation이거나 적어도 그의 일부이다.
PUT 메서드를 이용해 "welcome"이란 텍스트를 전송해서, greeting 리소스의 representation을 업데이트하는 경우,
클라이언트가 서버로 전송한 "welcome"은 representation이다.
업데이트가 성공하여 서버가 "성공적으로 업데이트되었습니다"라는 메시지를 응답이 payload로 돌려보냈다면 이 메시지 역시 representation이다.

## 참고

* https://blog.npcode.com/2017/04/03/rest%EC%9D%98-representation%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80/
* https://bcho.tistory.com/953
