# Spring EL(Spring Expression Language)

* 객체 그래프를 조회하고 조작하는 기능을 제공한다.
* Unified EL과 비슷하지만, 메소드 호출을 지원하며, 문자열 템플릿 기능도 제공한다.

Unified EL 예제

```jsp
<c:if test="${sessionScope.cart.numberOfItems > 0}">
...
</c:if>
```

* OGNL, MVEL, JBOss EL 등 자바에서 사용할 수 있는 여러 EL이 있지만, SpEL은 모든 스프링 프로젝트 전반에 걸쳐 사용할 EL로 만들었다.
* 스프링 3.0 부터 지원.

## SpEL 구성

* ExpressionParser parser = new SpelExpressionParser()
* StandardEvaluationContext context = new StandardEvaluationContext(bean)
* Expression expression = parser.parseExpression(“SpEL 표현식”)
* String value = expression.getvalue(context, String.class)

## 문법 * #{“표현식"}

* ${“프로퍼티"}
* 표현식은 프로퍼티를 가질 수 있지만, 반대는 안 됨.
  * #{${my.data} + 1}
* 레퍼런스 참고

```java
@Value("#{1 + 1}")
int value;

@Value("${'hello ' + 'world'}")
String greeting;

@Value("#{1 eq 1}")
boolean trueOfFalse;

@Value("hello")
String hello;

// property 참조
@Value("${my.value}") // properties에 있는 값을 참조하여 값을 가져온다.
int myValue;

@Value("#{${my.value} eq 100}") // 표현식 안에 property값을 가져와서 사용할 수 있다.
boolean isMyValue100;

@Value("#{sample.data}") // sample이라는 빈에 있는 값을 참조할 수 있다.
int sampleData;
```

## 실제로 어디서 쓰나?

* @Value 애노테이션
* @ConditionalOnExpression 애노테이션
* 스프링 시큐리티
  * 메소드 시큐리티, @PreAuthorize, @PostAuthorize, @PreFilter, @PostFilter
  * XML 인터셉터 URL 설정
  * ...
* 스프링 데이터
  * @Query 애노테이션
* Thymeleaf
* ...
