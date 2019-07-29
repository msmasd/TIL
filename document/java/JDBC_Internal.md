# JDBC Internal - 타임아웃의 이해

성능 문제나 장애가 발생할 때 중요하게 살펴보는 부분(tier)는 WAS와 DBMS입니다.

WAS와 DBMS사이에서도 이슈가 발생하는데, Java 애플리케이션을 기준으로 말하면 DBCP와 JDBC입니다.

## 어느 날 DDoS 공격, 그 뒤로 먹통된 WAS

다음과 같은 사고가 일어났다고 가정해보자.

DDos 공격으로 서비스 전체가 정상적으로 동작하지 않았다. L4가 정상적으로 동작하지 않아 네트워크가 단절되었고, 이로 인해 WAS도 동작 불능 상태에 빠졌다. 이후 보안팀에서는 DDoS 공격을 전부 차단했고, 네트워크도 정상으로 복구되었다. 그러나 WAS는 여전히 동작 불능 상태이다.

서비스팀에서는 WAS의 ThreadDump를 통해 JDBC의 API 호출 중에 WAS가 정지해 있음을 확인했다. 10분이 지나고 20분이 지나도 WAS는 여전히 정지 상태(WAITING)였고, 서비스는 정상으로 동작하지 않았다. 그런데 30분이 지날 무렵 갑자기 Exception을 발생시키면서 서비스가 복구되었다.

QueryTimeout 값도 3초로 설정되어 있는데 왜 30분씩이나 WAS가 정지 상태에 있었으며, 30분이 지나니 왜 정상적으로 WAS가 동작했던 것일까?

*정답은 JDBC의 타임아웃 과정을 이해하면 알 수 있다*

## 왜 JDBC 드라이버에 대해서 알아야 하는가?

JDBC는 DBMS에 접근하기 위한 표준 API이다. Sun은 4가지 타입의 드라이브를 정의하는데, NHN에서 주로 사용하는 것은 Type4 형식이다. JDBC Type4드라이버는 Java로만 작성되어 있으며, Java 애플리케이션에서 소켓을 이용해 DBMS와 통신한다.

![image1_JDBC_Type4](https://github.com/msmasd/TIL/blob/master/document/java/image/image1_JDBC_Type4.PNG)

Type4 드라이버는 소켓을 통해 바이트 스트림을 처리하기 때문에 HttpClient 같은 네트워크 라이브러리와 근본적으로 동작이 같다. 즉 많은 CPU 자원을 소모하고, ResponseTime의 손해가 있으며, 다른 네트워크 라이브러리가 가지고 있는 장애 포인트를 동일하게 가지고 있다. HttpClient를 사용한 경험이 있다면 타임아웃 값을 제대로 설정하지 않아 장애(hang)가 발생한 상황을 겪어 보았을 것이다. Type4 드라이버 역시 SocketTimeout값을 제대로 설정하지 않으면 동일한 장애가 발생할 수 있다.

JDBC 드라이버의 SocketTimeout 값을 어떻게 설정하면 좋을지, 그리고 무엇을 고려해야 하는지 알아보자.

## WAS와 DBMS의 통신 시 타임앙수 계층

![image2_TimeoutTier](https://github.com/msmasd/TIL/blob/master/document/java/image/image2_TimeoutTier.PNG)

WAS와 DBMS와 통신 시 타임아웃 계층을 단순화한 것이다.

상위 레벌의 타임아웃은 하위 레벨의 타임아웃에 의존성을 가지고 있다. 하위 레벨의 타임아웃이 정상으로 동작해야 상위 레벨의 타임아웃도 정상으로 동작한다. 예를들어, JDBC Driver SocketTimeout이 정상으로 동작하지 않으면, 그보다 상위 레벨의 타임아웃인 StatementTimeout과 TransactionTimeout도 정상으로 동작하지 않는다.

"StatementTimeout을 설정했는데도 네트워크 장애가 발생했을 때, StatementTimeout이 동작하지 않아 애플리케이션이 장애 상황에서 회복되지 않았어요"란 문의를 많이 받았다. StatementTimeout은 네트워크 연결 장애에 대한 타임아웃을 담당하는 것은 아니다. StatementTimeout은 Statement 한 개의 수행시간을 제한하는 기능만 담당한다. 네트워크 장애에 대비하는 타임아웃은 JDBC Driver SocketTimeout이 처리해야한다.

JDBC Driver SocketTimeout은 OS의 SocketTimeout 설정에 영향을 받는다. JDBC Driver SocketTimeout을 설정하지 않아도 네트워크 장애 발생 이후 30분이 지나면 JDBC Connection Hang이 복구되는 것은 OS의 SocketTimeout 설정 때문이다.

그림 2에서 DBCP Connection Pool이 타임아웃 계층과 분리되어 왼쪽에 있는것을 볼 수 있다. DBCP는 Connection을 생성하고 관리하는 일을 하며, 타임아웃 처리에는 관여하지 않는다. DBCP 내부에서 Connection을 생성하거나 Connection 유효성을 확인하려 Validation Query를 보낼때에는 SocketTimeout이 영향을 주지만 애플리케이션에 직접적인 영향을 주지 않는다.

단, 애플리케이션 로직에서 DBCP에 getConnection() 메서드를 호출할 때 Connection을 애플리케이션이 얻을 때까지의 타임아웃을 지정할 수 있다. 하지만 이것은 JDBC의 ConnectionTimeout과는 무관하다.

## TransactionTimeout이란?

TransactionTimeout은 프레임워크나 애플리케이션 레벨에서 유효한 타임아웃이다.

TransactionTimeout은 "StatementTimeout X N(Statement 수행 수) + α(가비지 컬렉션 및 기타)"라고 할 수 있다. 전체 Statement 수행시간을 허용할 수 있는 최대 시간 이내로 제한하려 할 때 TransactionTimeout을 사용한다.

가령 Statement 한 개를 수행할 때 0.1초가 필요하다면, 몇 개 안되는 Statement를 수행할 때에는 문제가 없다. 그러나 Statement 10만개를 수행할 때에는 일만 초(약7시간)가 필요하다. TransactionTimeout은 이런 경우에 사용할 수 있다.

## StatementTimeout 이란?

Statement 하나가 얼마나 오래 수행되어도 괜찮은지에 대한 한계 값이다. JDBC API인 Statement에 타임아웃 값을 설정하며, 이 값을 바탕으로 JDBC 드라이버가 StatementTimeout을 처리한다. JDBC API인 java.sql.Statement.setQueryTimeout(int timeout) 메서드로 설정.

## JDBC 드라이버의 SocketTimeout이란?

JDBC Driver Type4는 소켓을 사용하여 DBMS에 연결하는 방식이고, 애플리케이션과 DBMS 사이의 ConnectionTimeout 처리는 DBMS에서 하지 않는다.

JDBC 드라이버의 SocketTimeout 값은 DBMS가 비정상으로 종료되었거나 네트워크 장애(기기 장애 등)가 발생했을 때 필요한 값이다. TCP/IP의 구조상 소켓에는 네트워크 장애를 감지할 수 있는 방법이 없다. 그렇기 때문에 애플리케이션은 DBMS와의 연결 끊김을 알 수 없다. 이럴 때 SocketTimeout이 설정되어 있지 않다면 애플리케이션은 DBMS로부터의 결과를 무한정 기다릴수도 있다(이러한 Connection을 Dead Connection이라고 부른다.)

단, SocketTimeout값을 Statement의 수행 시간 제한을 위해 사용하는 것은 바람직하지 않다. 그러므로 SocketTimeout값은 StatementTimeout값보다는 크게 설정해야 한다. SocketTimeout값이 StatementTimeout보다 작으면, SocketTimeout이 먼저 동작하므로 StatementTimeout 값은 의미가 없게 된다.

## OS 레벨 SocketTimeout 설정

SocketTimeout이나 ConnectionTimeout을 설정하지 않으면 네트워크 장애가 발생해도 애플리케이션이 대부분 이를 감지할 수 없다. 따라서 연결이 되거나 데이터를 읽을 수 있을 때까지 애플리케이션이 무한정 기다리게 된다. 그러나 서비스에서 발생한 실제 장애 상황에서는 30분 후에 애플리케이션이 재연결을 시도하여 문제가 해결되는 경우가 많다. OS에서도 SocketTimeout 시간을 설정할 수 있기 때문이다.
