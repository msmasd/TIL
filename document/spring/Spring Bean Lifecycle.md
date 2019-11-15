# Spring Bean Lifecycle

1. instantiate (인스턴스화?)
2. populate bean properties 빈 프로퍼티 덧붙이기
3. calls setBeanName() method of BeanNameAware: BeanNameAware의 setBeanName 함수 호출
4. calls setBeanFactory() of BeanFactoryAware: BeanFactoryAware의 setBeanFactory 함수 호출
5. calls setApplicationContext() of ApplicationContextAware: ApplicationContextAware의 setApplicationContext 함수 호출
6. PreInitialization (BeanPostProcessors) -> PostConstruct
7. afterPropertiesSet() method of InitaializingBeans: InitalizingBeans의 afterPropertiesSet() 함수 호출?
8. Custom init Method: 사용자화 초기 메서드 호출?
9. Post Initialization(BeanPostProcessors)
10. Bean is Ready to use: 빈 사용준비
11. Spring Container shutdown
12. @PreDestory (JDK annotation)
13. DisposableBean interface (destory())
14. Custom 'destory' method

## InitializingBean and DisposableBean callback interface

### InitializingBean interface

* org.springframework.beans.factory 패키지에 있고
* 하나의 함수가 선언되어있다. 이 함수는 우리가 초기화 관련된 코드를 추가할 수 있다.
* 어떤 빈이든 이 인터페이스를 구현할려면 afterPropertiesSet함수를 구현해야한다. (``void afterPropertiesSet() throws Exception``)

### DisposableBean interface

위에 있는 인터페이스와 내용이 거의 동일하고, 다른점은 destory될때 호출되는 부분이다. 함수는 destory함수를 구현해야한다. (``void destory() throws Exception``)

## annotaion(@PostConstruct, @PreDestroy)

어노테이션으로도 초기화와 destory되기전에 구현할 내용을 실행할 수 있다.
@PostConstruct는 JSR-250스펙에 구현된것이기 때문에 JSR-250을 구현한 어느 프레임워크든지 사용가능하다.
어노테이션은 어노테이션만 붙이고 바로 사용하면 된다.

## 순서

1. @PostConstruce
2. afterPropertiesSet()
3. initMethod(빈에서 정의한 init에 대한 함수)

## 참고

* http://www.wideskills.com/spring/spring-bean-lifecycle
* http://wonwoo.ml/index.php/post/1820
