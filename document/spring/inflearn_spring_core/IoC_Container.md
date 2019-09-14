# IoC Container

Inverion of Control: 의존 관계 주입(Dependency Injection)이라고도 하며, 어떤 객체가 사용하는 **의존 객체를 직접 만들어 사용하는게 아니라, 주입 받아 사용하는 방법**을 말함.
(클래스안에서 클래스 변수를 생성자를 직접 만들어서 사용하는 것이 아니아 생성자를 통해서 사용하는 것)

스프링 IoC 컨테이너

* BeanFactory
* 애플리케이션 컴포넌트의 중앙 저장소
* **빈 설정 소스**로 부터 **빈 정의**를 읽어드리고, **빈을 구성하고 제공**한다.

## 빈

* 스프링 IoC 컨테이너가 관리하는 객체.
* 장점
  * 의존성 관리
  * 스코프
    * 싱글톤: 하나(애노테이션을 특별히 주지않으면 싱글톤)
    * 프로토타입: 매번 다른 객체
  * 라이프사이클 인터페이스

왜 빈으로 등록하는가?
-> 의존성 주입때문이기도 하고, 의존성 주입을 받을려면 빈이 되어야 한다.
-> 빈의 scope떄문이기도 함. -> 북서비스를 여러개 만들필요가 없을때, 즉 싱글톤으로 관리하고 싶을때, IoC컨테이너를 사용하면 편하다.(일반적으로 IoC 컨테이너는 싱글톤으로 사용)

@PostConstruct
이 애노테이션을 사용하면 이 빈이 만들어졌을때 작업을 진행할 수 있다.

가짜객체를 만들어서 테스트를 진행할 수 있다.

## ApplicationContext

이거도 인터페이스, 이거를 주로 사용

* BeanFactory를 상속받아서 만들어짐(IoC컨테이너의 기능을 가지고 있으면서도 아래 기능들이 더 있음)
* 메시지 소스 처리 가능(i18n)
* 이벤트 발행 기능
* 리소스 로딩 기능
* ...

IoC 컨테이너 안에 들어있는 객체를 빈.
스프링 초기에는 xml로 빈을 정의했지만, 추후에는 애노테이션 기반으로 정의

빈에 등록을 하고(@Service, @Container)
등록된 빈을 가져와 사용하기도 한다(@Autowired)

bean factory라는 인터페이스가 있는데, 다양한 구현체들이 있는데, 이 인터페이스가 IoC의 핵심

스프링 IoC 컨테이너의 역활

* 빈 인스턴스 생성
* 의존 관계 설정
* 빈 제공

ApplicationContext

* ClassPathXmlApplicationContext (XML)
* AnnotationConfigApplicationContext (java)

빈 설정

* 빈 명세서
* 빈에 대한 정의를 담고 있다.
  * 이름
  * 클래스
  * 스코프
  * 생성자 아규먼트(constructor)
  * 프로퍼트 (setter)
  * ...

의존성 주입을 하지않아도 bean에 있으면 Autowired로 땡길수 있다.
ApplicationConfig를 사용할 수 있다.
이 ApplicationConfig도 스프링이 알아서 해준다.
-> @SpringBootApplication에서 @Configuration와 @ComponentSanc이 있다.

bean설정 관련해서는 방법이 2가지 있다.

1. xml
   1. ClassPathXmlApplicationContext클래스를 사용하여 xml파일의 내용을 읽어오는데, 여기서 bean에 대한 설정 정의한것을 가져온다.
2. java파일의 @Configuration
   1. AnnotationConfigApplicationContext클래스를 사용하여 java파일의 내용을 읽어오는데, 여기서 bean에 대한 설정 정의한것을 가져온다.

공통적으로 bean들에 대해서 일일이 명시할 수 있지만, ComponentScan을 사용할 수 있다.
(명시할때는 어노테이션의 Container들을 사용할 필요 없다.)
ComponentScan은 base-package와 비슷하게 패키지 안에서 어노테이션으로 명시된 컴포넌트들(@Repository, @Service 등)을 bean으로 인식하고 context에 있다.

IoC 컨테이너 안에 있으면 이미 등록되어있는 빈의 이름으로 의존성 주입을 @Autowired로 할 수 있다.