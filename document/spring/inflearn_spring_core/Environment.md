# Environment

ApplicationContext는 beanFactory만 하는것이 아니다.
EnvironmentCapable에서 프로파일에 대한것을 보자.

프로파일은 빈들의 묶음
스프링에 있는 프로파일도 마찬가지. 환경이다.
각각의 환경에 따라 다른 빈들을 써야하는경우, 어떤 환경에서만 특정 빈을 써야하는 경우 -> Environment를 사용해서 한다.

프로파일과 프로퍼티를 다루는 인터페이스.
ApplicationContext extends EnvironmentCapable

* getEnvironment() // EnvironemtCapable에서 옴
  * getActiveProfiles -> 현재 활성화된 Profiles를 받음

프로파일

* 빈들의 그룹
* Environment의 역할은 활성화할 프로파일 확인 및 설정

프로파일 유즈케이스

* 테스트 환경에서는 A라는 빈을 사용하고, 배포 환경에서는 B라는 빈을 쓰고 싶다.
* 이 빈은 모니터링 용도니까 테스트할 때는 필요가 없고 배포할 때만 등록이 되면 좋겠다.

프로파일 정의하기

* 클래스에 정의
  * @Configuration @Profile(“test”)
  * @Component @Profile(“test”)
* 메소드에 정의
  * @Bean @Profile(“test”)

프로파일 설정하기

* -Dspring.profiles.avtive=”test,A,B,...”
* @ActiveProfiles (테스트용)

프로파일 표현식

* ! (not)
* & (and)
* | (or)

프로퍼티

* 다양한 방법으로 정의할 수 있는 설정값
* key-value쌍으로 있다.
* 계층형으로 있다(우선순위가 있다.)
* Environment의 역할은 프로퍼티 소스 설정 및 프로퍼티 값 가져오기

```java
Environment envirnoment = ctx.getEnviroment();
String property = environment.getProperty("app.name");
```

프로퍼티에는 우선 순위가 있다.

* StandardServletEnvironment의 우선순위
  * ServletConfig 매개변수
  * ServletContext 매개변수
  * JNDI (java:comp/env/)
  * JVM 시스템 프로퍼티 (-Dkey=”value”)
  * JVM 시스템 환경 변수 (운영 체제 환경 변수)

@PropertySource

* Environment를 통해 프로퍼티 추가하는 방법 ``@PropertySource("classpath:/app.properties")`` app.properties는 파일명

스프링 부트의 외부 설정 참고

* 기본 프로퍼티 소스 지원 (application.properties)
* 프로파일까지 고려한 계층형 프로퍼티 우선 순위 제공

```java
@Value("${app.name}")
String appName; // 여기에 바인딩이 된다.
```