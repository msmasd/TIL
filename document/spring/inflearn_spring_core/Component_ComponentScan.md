# Component And ComponentScan

컨포넌트 스캔(ComponentScan) 주요 기능

basePackages: 문자열(이거는 타입세이프하지않는다.)
basePackageClasses
여기값에 전달된 클래스를 기준으로 컴포넌트 스캔을 시작한다(이거는 타입세이프하다)
기본값은 이 ComponentScan을 붙이고 있는 configuration부터 시작한다.

* 스캔 위치 설정
* 필터: 어떤 애노테이션을 스캔 할지 또는 하지 않을지
  * excludeFilters
    * default: autoConfigurationFilter 등등 있지만 대략적으로 추측..? 제대로 알지않아도 괜찮

## @Component

빈으로 등록하는 것

* @Repository
* @Service
* @Controller
* @Configuration

동작 원리

* @ComponentScan은 스캔할 패키지와 애노테이션에 대한 정보
* 실제 스캐닝은 ConfigurationClassPostProcessor라는 BeanFactoryPostProcessor에 의해 처리 됨.

BeanFactoryPostProcessor: 다른 모든 빈 등록전에 컴포넌트스캔을 하여 등록해준다.

### 펑션을 사용한 빈 등록

이렇게 작업을 하면 앱 구동시에는 성능이 빠르지만, 컴포넌트스캔이 아닌것이 아니고 직접 빈에 등록하는 빈들에 대해서는 이렇게 한다.

```java
// componentScan 범위에 없는 클래스를 등록
public static void main(String[] args) {
  new SpringApplicationBuilder()
      .sources(DemospringApplication.class)
      .initializers((ApplicationContextInitializer<GenericApplicationContext>)
      applicationContext -> {
        applicationContext.registerBean(MyBean.class);
      })
      .run(args);
}
```
