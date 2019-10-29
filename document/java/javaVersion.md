# Java Version

## Java 8

Java 8의 큰 특징은  lambda expression이 가능해 진 것이다.

lambda expression
stream API
date and time API
Nashhorn JavaScript 엔진

## Java 9

큰 특징은 Java Jigsaw 프로젝트가 중요하다. 이 Jigsaw 프로젝트는 자바 플랫폼 모듈 시스템을 만들어내는 것에 의미가 있다.
즉, Java Module System은 안정적인 구성과 강력하고 유연한 캡슐화를 제공한다는 구체적인 목표를 가지고 있다. 이를 통해 응용 프로그램 개발자, 라이브러리 개발자 또는 Java SE Platform 개발자는 확장 가능한 플랫폼을 만들고 플랫폼 무결성을 높이며 성능을 향상 시킬 수 있다.

### Module?

code: type을 포함하고 있는 package (Java Class들, interface들)
data: resource와 다른 종류의 정적정보

요약한다면

Class는 field와 method를 포함한다.
Package는 Class와 Enum, Interface, 설정파일들을 포함한다.
Module은 Package와 다른 데이터 자원을 포함한다.

### Java 9 특징

Java Module
Tools
JShell
Unified JVM Logging
HTML6 Javadoc
Language Update
try-with-resource 향상
private interface Method
interface에 private method, private static method를 추가로 제공함
Diamond Operator
<>(다이아몬드 연산자)는 Java7에서 등장했지만, 익명클래스에서는 제한적이였는데, Java9에서 익명클래스를 ?로 표시하여 작업할 수 있다.
New Code Libraries
Process API
Platform Logging API and Service
CompletableFutre API 강화
Reactive Streams - Flow API
publish-subscribe 프레임워크를 지원하는 java.util.concurrent.Flow에서 Reactive Stream을 소개한다.
Collections를 위한 팩토리 메소드
강화된 Deprecation
Stack-Walking API
etc
Stream 개선
iterate(), takeWhile()/ dropWhile(), ofNullable()
Optional 개선


### 참고
https://www.popit.kr/%EB%82%98%EB%A7%8C-%EB%AA%A8%EB%A5%B4%EA%B3%A0-%EC%9E%88%EB%8D%98-java9-%EB%B9%A0%EB%A5%B4%EA%B2%8C-%EB%B3%B4%EA%B8%B0/

## Java 10

### Java 10 특징

Local-Variable Type Interface
로컬변수를 선언할 때 타입추론을 이용하여 명시적으로 타입선언 없이도 변수를 선언할 수 있게 되었다.
Consolidate the JDK Forest into a Single Repository
JDK 저장소들을 단일 저장소로 옮겼다고 함.
Garbage-Collector Interface
가비지콜렉터 인터페이스를 도입하였다.
Parallel Full GC for G1
이전 JDK의 G1 가비지 컬렉터는 Full GC를 피할수 있게끔 설계 되었지만, 병행 컬렉터 작업에서 충분할 만큼 빠르게 메모리 반환을 하지 못한다면 Full GC가 발생한다.
-XX:ParallelGCThreads 옵션으로 스레드의 수를 조정할 수 있으며 이는 Young과 Mixed의 스레드의 수에도 적용된다.
Application Class-Data Sharing
기존에 AppCDS(Class-Data Sharing) 기능은 상업용으로  Oracle JDK에서만 제공되었으나, 오픈소스화 되어 Open JDK에도 사용할 수 있게 되었다.
Thread-Local Handshakes
GC가 발생하기 위해 흔히 말하는 “stop-the-world”가 발생하는데, GC를 수행하기 위해 GC를 실행하는 쓰레드를 제외하고 모든 쓰레드가 작업을 멈추는 것을 의미한다.
이전과 같이 모든 쓰레드들이 동시에 멈추지 않고, 쓰레드를 개별로 멈출수 있게 되었고 VM safepoint 수행 없이도 개별 쓰레드에서 콜백 실행이 가능해졌다.
Root Certificate
JDK에서 최상위 인증 기관 CertificationAuthority(CA)의 기본세트를 제공한다.
Time-Based Release Versioning

### 참고
https://jusungpark.tistory.com/58

## Java 11

### Java 11 특징

Nest-based access controls(Nest 기반 접근 제어)
Nest는 접근 제어 컨텍스트로 논리적으로 같은 클래스를 분리된 클래스로 컴파일할 수 있게 해줍니다. 그러면 다른 클래스의 private 멤버에 getter/setter 없이 바로 접근 가능합니다. 여러 클래스를 하나의 클래스처럼 묶어줄 수 있는 기술로 보입니다.
새로운 가비지 컬렉터
Flight Recorder
자바 애플리케이션과 HotSpot JVM의 문제 해결을 위한 오버해드가 낮은 데이터 수집 프레임워크(이전에는 유로 기능이지만, 오픈소스로 공개)
새로운 표준 HTTP 라이브러리
java.net.http 패키지의 새로운 모듈로 flow 기반의 HTTP/1.1과 HTTP/2를 지원합니다. 자바9와 자바10에서 사용되었던 jdk.incubator.http 패키지가 표준화되어 java.net.http 패키지로 추가되었습니다.
TLS 1.3
TLS(SSL의 표준 이름)의 새로운 버전을 구현
람다에서의 var 변수
람다에서도 var 타입 추론이 가능

