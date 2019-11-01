# JavaBeanValidation

일반적으로 데이터 검증은 여러 계층에 걸쳐서 이루어지게 됩니다.
거의 동일한 내용의 검증로직이 각 계층별로 구현된다면 그것은 중복이고 낭비가 심한 작업일것입니다.
또한 그러한 경우 각 계층별로 구현된 검증로직간 불일치로 인하여 오류가 발생하기도 쉽습니다.

데이터 검증을 위한 로직을 도메인 모델 자체에 묶어서 표현하는 방법이 있습니다.
Java에서는 Bean Validation이라는 이름으로 애노테이션을 데이터 검증을 위한 메타데이터로 사용하는 방법을 제시하고 있습니다.
(현재 BeanValidation이 2.0이 최신(2019.11.01 기준)) (공식문서 (https://docs.jboss.org/hibernate/beanvalidation/spec/2.0/api/))

## Hibernate Validator

Hibernate Validator는 Bean Validation명세에 대한 구현체입니다.

BeanValidation 2.0에 대한 구현은 Hibernate Validation 6.0.1.Final이며 Spring Boot 2.0 이상에서 이것을 사용하고 있습니다.

## 제약조건의 작성

BeanValidation은 애노테이션을 사용하여 제약조건을 명시하게 된다.
애노테이션 리스트는 (https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/#section-builtin-constraints)서 확인.

## 제약조건에 대한 유효성 검증

제약조건의 확인은 javax.validation.Validator를 사용해서 이루어집니다.

가장 쉽고 빠르게 Validator를 가져오는 방법은 ValidatorFactory를 사용하는 방법입니다.
그리고 validate()를 사용해서 빈의 유효성검증을 실행합니다.
제약조건을 위반한 내용은 constraintViolation 인터페이스로 반환됩니다.



## 참고

* 블로그: https://medium.com/@gaemi/java-%EC%99%80-spring-%EC%9D%98-validation-b5191a113f5c