# WebClient

spring5에서 소개하는 **reactive web client** 이다.
*WebTestClient*는 *WebClient*를 테스트하기 위해 사용됌

* 정의
  * web requests를 수행하기 위한 메인 엔트리 포인트(진입점)를 표현하기 위한 인터페이스이다.
  * WebClient는 Spring Web Reactive module의 하나의 파트이고, 기존 RestTemplate을 대체하기 위해 만들어졌다.
  * WebClient는 HTTP/1.1 protocol에서 동작하는 reactive, non-blocking solution이다.
* Dependencies
  * *spring-boot-starter-webflux*
  * code
```java
dependencies {
    compile 'org.springframework.boot:spring-boot-starter-webflux'
    compile 'org.projectreactor:reactor-spring:1.0.1.RELEASE'
}
```

* 사용방법
  * WebClient Instance 생성
    * 기본 세팅의 초기화
      * ```WebClient client1 = WebClient.create();```
    * base URI가 있는 초기화
      * ```WebClient client1 = WebClient.create("http://localhost:8080");```
    * DefaultWebClientBuilder 클래스를 사용한 초기화
```java
WebClient client3 = WebClient
  .builder()
    .baseUrl("http://localhost:8080")
    .defaultCookie("cookieKey", "cookieValue")
    .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
    .defaultUriVariables(Collections.singletonMap("url", "http://localhost:8080"))
  .build();
```
  * Request 준비
    * method 호출이나 get, post와 같은 단축함수를 호출함으로써, request의 HTTP method를 명확히 해아한다.
      * ```WebClient.UriSpec<WebClient.RequestBodySpec> request1 = client3.method(HttpMethod.POST);```
      * ```WebClient.UriSpec<WebClient.RequestBodySpec> request2 = client3.post();```
    * URL을 제공한다. *uri* API로 넘겨줄 수 있다.
```java
WebClient.RequestBodySpec uri1 = client3
  .method(HttpMethod.POST)
  .uri("/resource");

WebClient.RequestBodySpec uri2 = client3
  .post()
  .uri(URI.create("/resource"));
```
    * request body, content type, length, cookies or headers 등 우리가 필요한 것을 세팅할 수 있다
      * request body를 채우는 방법은 두가지가 있다
        * BodyInserter
        * Publisher에게 일을 위임하는 방법
```java
WebClient.RequestHeadersSpec requestSpec1 = WebClient
  .create()
  .method(HttpMethod.POST)
  .uri("/resource")
  .body(BodyInserters.fromPublisher(Mono.just("data")), String.class);

WebClient.RequestHeadersSpec<?> requestSpec1 = WebClient
  .create("http://localhost:8080")
  .post()
  .uri(URI.create("/resource"))
  .body(BodyInserters.fromObject("data"));
```

BodyInserter..?

    * MultiValueMap도 사용 가능
```java
LinkedMultiValueMap map = new LinkedMultiValueMap();

map.add("key1", "value1");
map.add("key2", "value2");

BodyInserter<MultiValueMap, ClientHttpRequest> inserter2
  = BodyInserters.fromMultipartData(map);
```

    * single Object도 사용 가능
```java
BodyInserter<Object, ReactiveHttpOutputMessage> inserter3
  = BodyInserters.fromObject(new Object());
```

body 세팅 후, headers, cookies, acceptable media types등 세팅 가능하다.

* Response 얻기
  * *exchange* 나 *retrieve* 메서드를 통하여 얻을 수 있다.
  * 두개는 return type이 다르다.
    * exchange 메서드는 *ClientResponse*(status, headers를 포함)
    * retrieve 메서드는 body를 직접적으로 fetch해오는 가장 짧은 방법이다.

## Mono와 Flux

Mono는 0-1개의 결과만을 처리하기 위한 Reactor의 객체이고, Flux는 0-N개인 여러 개의 결과를 처리하는 객체이다.
Reactor를 사용해 일련의 스트림을 코드로 작성하다 보면 보통 여러 스트림을 하나의 결과를 모아줄 때 Mono를 쓰고, 각각의 Mono를 합쳐서 여러 개의 값을 처리하는 Flux로 표현할 수도 있습니다.
Mono와 Flux 모두 Reactive Stream의 Publisher 인터페이스를 구현하고 있으며, Reactor에서 제공하는 풍부한 연산자들(operators)의 조합을 통해 스트림을 표현할 수 있습니다.
예를 들어 flux에서 하나의 결과로 값을 모아주는 reduce연산자는 Mono를 리턴하고, Mono에서 flatMapMany라는 연산자를 사용하면 하나의 값으로부터 여러 개의 값을 취급하는 Flux를 리턴할 수 있습니다.
그리고 Publisher인터페이스에서 정의된 subscribe 메서드를 호출함으로써 Mono나 Flux가 동작하도록 할 수 있습니다.

## 참고
* https://www.baeldung.com/spring-5-webclient
* https://akageun.github.io/2019/06/23/spring-webflux-4-webclient.html
* Mono와 Flux 카카오 테크: https://tech.kakao.com/2018/05/29/reactor-programming/
* https://javacan.tistory.com/entry/Reactor-Start-2-RS-just-generate
* Mono: https://projectreactor.io/docs/core/release/reference/#mono
* Flux: https://projectreactor.io/docs/core/release/reference/#flux
* WebTestClient: https://docs.spring.io/spring/docs/current/spring-framework-reference/pdf/testing-webtestclient.pdf
