# Validation

org.springframework.validation.Validator
애플리케이션에서 사용하는 객체 검증용 인터페이스.
bean Validation은 JavaEE표준 스펙 -> 애노테이션으로 빈에 있는 데이터를 검증한다.

두가지 메서드를 구현해야한다.

1. support
   1. 내가 검증해야 하는 클래스인지 확인하는 메서드
2. validate
   1. 여기서 실제적으로 검증이 일어나는 부분
   2. ValidationUtils에서 검증할 함수들을 제공한다.
      1. rejectIfEmptyOrWhitespace(error, field, errorCode, defaultMessage) -> errorCode는 key / defaultMessage는 errorCode로 값을 못찾았을때 출력하는 기본 메시지
   3. errors에 직접 넣을수도 있다.
      1. error.reject에 에러코드를 넣을 수 있다.(특정 필드의 에러이면 필드를 넣어줄수 있다.(rejectField))

특징

* 어떤한 계층과도 관계가 없다. => 모든 계층(웹, 서비스, 데이터)에서 사용해도 좋다.
* 구현체 중 하나로, JSR-303(Bean Validation 1.0)과 JSR-349(Bean Validation 1.1)을 지원한다. (LocalValidatorFactoryBean)
* DataBinder에 들어가 바인딩 할 때 같이 사용되기도 한다.

인터페이스

* boolean supports(Class clazz): 어떤 타입의 객체를 검증할 때 사용할 것인지 결정함
* void validate(Object obj, Errors e): 실제 검증 로직을 이 안에서 구현
  * 구현할 때 ValidationUtils 사용하며 편리 함.
  * 해당 에러에 대해서 errors에 데이터를 넣어준다.

스프링 부트 2.0.5 이상 버전을 사용할 때

* LocalValidatorFactoryBean 빈으로 자동 등록
  * @Autowired를 통해 Validator를 받을 수 있다.
  * 즉 Validator가 없어도 Validation을 할 수 있다. -> 뭔가 복잡한 검증이 필요한 경우에는 직접 구현해야한다.
* JSR-380(Bean Validation 2.0.1) 구현체로 hibernate-validator 사용.
* https://beanvalidation.org/
