Spring

정의
Spring은 정확히 Spring Framework이고, 자바 엔터프라이즈 개발을 위한 Open Source 애플리케이션 프레임워크
종속 객체를 생성해주고, 조립할 수 있는 프레임워크
자바SE로 된 객체(POJO)를 자바EE에 의존적이지 않게 연결해준다.

주요특징
1. DI(Dependency Injection) 의존성 주입
    1. 각각의 계층이나 서비스들 간에 의존성이 존재할 경우 프레임워크가 서로 연결해준다.
2. AoP(Aspect Oriented Programing) 관점 지향 프로그래밍
    1. 트랜잭션이나 로깅, 보안과 같이 여러 모듈에서 공통적으로 사용하는 기능의 경우 해당 기능을 분리하여 관리할 수 있다.
3. IoC(Inversion of Control) 제어의 역전
4. POJO사용
5. 애플리케이션 객체의 생명 주기와 설정을 포함하고 관리한다는 점에서 일종의 “컨테이너”라고 할 수 있다.
    1. iBatis, myBatis나 Hibernate 등 완성도가 높은 데이터베이스 처리 라이브러리와 연결할 수 있는 인터페이스를 제공한다.
6. 트랜잭션 관리 프레임워크
    1. 추상화된 트랜잭션 관리를 지원하며 설정파일(xml, java, property 등)을 이용한 선언적인 방식 및 프로그래밍을 통한 방식을 모두 지원한다.
7. MVC패턴
    1. 웹 프로그래밍 개발 시 거의 표준적인 방식인 Spring MVC라 불리는 모델-뷰-컨트롤러패턴을 사용
    2. DispatcherServlet이 Controller 역활을 담당하여 각종 요청을 적절한 서비스에 분산시켜주며 이를 각 서비스들이 처리를 하여 결과를 생성하고 그 결과는 다양한 형식의 View 서비스들로 화면에 표시될 수 있다.
8. 배치 프레임워크
    1. 스프링은 특정 시간대에 실행하거나 대용량의 자료를 처리하는데 쓰이는 일괄 처리(Batch Processing)을 지원하는 배치 프레임워크를 제공한다. 기본적으로는 스프링 배치는 Quartz기반으로 동작
9. 즉 공통 부분의 소스 코딩이 용이하며 확정성도 매우 높다

Spring Module


1. Spring Core
    1. Spring 프레임워크의 근간이 되는요소. IoC(or DI)기능을 지원하는 영역을 담당
    2. BeanFactory를 기반으로 Bean 클래스들을 제어할 수 있는 기능 지원
2. Spring Context
    1. Spring Core 바로 위에 있으면서 Spring Core에서 지원하는 기능외에 추가적인 기능들과 좀 더 쉬운 개발이 가능하도록 지원
    2. JNDI, EJB등을 위한 Adaptor들을 포함
3. Spring DAO
    1. 지금까지 우리들이 일반적으로 많이 사용해왔던 JDBC기반하의 DAO개발을 좀 더 쉽고, 일관된 방법으로 개발하는 것이 가능하도록 지원
    2. Spring DAO를 이용할 경우 지금까지 개발하던 DAO보다 적은 코드와 쉬운 방법으로 DAO를 개발하는 것이 가능
4. Spring ORM
    1. Object Relation Mapping 프레임워크인 Hibernate, IBatis, JDO와의 결합을 지원하기 위한 기능
    2. Spring ORM을 이용할 경우 Hibernate, IBatis, JDO 프레임워크와 쉽게 통합하는 것이 가능
5. Spring AOP
    1. Spring 프레임워크에 Aspect Oriented Programming을 지원하는 기능. 이 기능은 AOP Aliance 기반하에서 개발
6. Spring Web
    1. Web Application 개발에 필요한 Web Application Context와 Multipart Request등의 기능을 지원
    2. 또한 Struts, Webwork와 같은 프레임워크의 통합을 지원하는 부분음 담당
7. Spring Web MVC
    1. Spring 프레임워크에서 독립적으로 Web UI Layer에 Model-View_Controller를 지원하기 위한 기능
    2. 지금까지 Struts, Webwork가 담당했던 기능들을 Spring Web MVC를 이용하여 대체하는 것이 가능하다. 또한 Velocity, Excel, PDF와 같은 다양한 UI기술들을 사용하기 위한 API를 제공


