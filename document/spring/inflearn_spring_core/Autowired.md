# @Autowir

필요한 의존 객체의 “타입"에 해당하는 빈을 찾아 주입한다.
@Autowired

* required: 기본값은 true (따라서 못 찾으면 애플리케이션 구동 실패)

사용할 수 있는 위치

* 생성자 (스프링 4.3 부터는 생략 가능)
  * @Autowired를 사용할 경우 여기서는 required를 사용할 수가 없다. 왜냐? 생성자는 무조건 빈 생성시에 만들어져야 하기 때문.
* 세터
  * 세터인경우에도 해당 세터에 해당하는 빈이 없는경우에는 에러가 발생한다.
  * 빈을 만들때, @Autowired 애노테이션이 있으면 이 애노테이션을 동작을 하면서 빈을 생성한다.(만약 이 값이 옵셔널이면 required=false를 넣어주면 에러가 발생하지않는다)
* 필드
* 세터와 동일

경우의 수

* 해당 타입의 빈이 없는 경우
* 해당 타입의 빈이 한 개인 경우
* 해당 타입의 빈이 여러 개인 경우
  * 빈 이름으로 시도,
    * 같은 이름의 빈 찾으면 해당 빈 사용
    * 같은 이름 못 찾으면 실패

같은 타입의 빈이 여러개 일 때

* @Primary *추천*
* 해당 타입의 빈 모두 주입 받기 -> ``List<BookRepository> bookRepositroies``
* @Qualifier (빈 이름으로 주입) -> 빈 이름은 smallcase로 시작되는 클래스 이름("keesunBookRepository")

동작 원리

* 첫시간에 잠깐 언급했던 빈 라이프사이클 기억하세요?
* BeanPostProcessor
  * 새로 만든 빈 인스턴스를 수정할 수 있는 라이프 사이클 인터페이스
  * 빈의 인스턴스를 만든다음에, 빈의 초기화하는 라이프사이클이 있다. 빈의 이니셜라이즈 라이프사이클 이후에 하는 라이프 사이클 콜백이 BeanPostProcessor
* AutowiredAnnotationBeanPostProcessor extends BeanPostProcessor
  * 스프링이 제공하는 @Autowired와 @Value 애노테이션 그리고 JSR-330의 @Inject 애노테이션을 지원하는 애노테이션 처리기.
