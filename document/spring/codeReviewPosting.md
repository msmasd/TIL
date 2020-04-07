# 코드리뷰 블로그 포스팅 리뷰

블로그에 있는 코드리뷰 포스팅 정리 글입니다.

## 배민 코드리뷰 적응기

* Entity
  * Lombok의 어노테이션으로 @Data 남용은 하지말자.
    * 실무에서는 @Data를 지양한다.
    * 무분별한 Setter의 남용을 막고, 도메인 기능에 대한 메소드들만 사용하도록 합시다.
  * paymentDate는 결제일시를 나타내기 위해 타입을 LocalDateTime으로 했는데, paymentDate라는 네이밍으로는 타입과 일치하지 않아 맞추는것이 좋다.
    * LocalDateTime이면 ~DateTime, LocalDate이면 ~Date
  * LocalDateTime 데이터를 웹에서 보여줄 때 yyyy-MM-dd HH:mm:ss 형태로 보여주고 싶어서 @JsonFormat을 사용했는데, 하지만 Entity를 건드리기보다 이 Entity를 건드리기보다 이 Entity의 ResponseDto에서 JsonFormat을 사용하여 데이터를 조작해야합니다.
    * Entity는 우리가 지켜야할 고유의 영역입니다. 만약 이 Entity를 쓰는 API가 신규로 추가되고 그 API에서는 yyyyMMdd HH:mm:ss로 포맷이 필요하면 어떻게 해야할까요? Entity를 변경하기 보다는 이 Entity를 사용하는 DTO를 사용하여 최대한 변경에 대응하기 쉽도록 합니다.
  * deleted 라는 필드는 삭제 여부를 나타내고, "Y", "N"으로 데이터를 두었습니다.
    * 하지만 이런 플래그를 사용할 때는 String을 사용하지 않습니다. 다른 문자열도 들어갈 수 있기 때문에 Boolean타입을 사용해야 합니다.
  * 연관관계를 맺고 있는 필드는 중간 중간에 위치하고 있어서 가독성이 떨어집니다. 실제로 확인할 칼럼을 보기가 어렵다.
    * 연관관계는 하단으로 내려서 가독성을 좋게 하는 것이 좋습니다.
  * NoArgsConstructor 접근권한을 주지 않았습니다.
    * JPA에서는 프록시를 생성을 위해서 기본 생성자를 반드시 하나를 생성해야 합니다.
      * 이때 접근 권한을 AccessLevel.PROTECTED로 설정하여 JPA에서의 Entity클래스 생성만 허용해줍니다.
* 동적 QueryDsl
  * Null 체크하고 검색타입에 따라 SQL문을 추가하듯이 작성했는데, 이렇게 하면 mybatis를 사용했다는 의견을 주심.. 그리고 if문 안에 또 if문이 있고 전체적으로 가독성이 떨어지는 소스
    * 해당 코드는 각각 조건에 따라서 함수를 만들고, 해당 함수에 조건이 만족하지 않으면 null을 리턴하여 처리.
* @Autowired 지양
  * 의존 관계를 설정할 때 대부분을 @Autowired를 사용
    * Spring Framework에 종속적이기 때문에 @Autowired는 지양하고, 아래와 같은 생성자를 통해 주입
```java
public class RestSalesController {
    private final SalesService salesService;

    public RestSalesController(SalesService salesService) {
        this.salesService = salesService;
    }
}
// 더 나아가면
@RequiredArgsConstructor
public class RestSalesController {
    private final SalesService salesService;
}
```

* 친절한 테스트 코드
  * 테스트 코드 작성중에 데이터를 저장하기 위해 메소드 내에 builder를 사용
    * 이렇게 하면 다른사람이 봤을 때 이해하기 쉽지 않다는 피드백
    * 테스트 결과를 확인하는 부분도 메소드로 고쳐야 할듯
  * JUnit에서는 강제하지 않지만, given, when, then 주석을 달고 명확하게 작성해야 합니다.
    * given: 테스트에 필요한 상황
    * when: 테스트하려는 일의 동작
    * then: when에서 발생한 일의 결과
    * 매번 달기 귀찮으면 template 사용
  * @Before, @After가 붙은 메소드는 모르는 사람이 봤을 때, 테스트의 전과 후에 어떤 일을 하는지 명확히 하기 위해 맨 위에 작성해주는 것이 좋음
* DTO  잘 사용하기
  * Dto는 사용에 따라 최대한 쪼개야 합니다.
    * 예를 들어 하나의 Entity를 용도에 따라 보여주는 부분이 다를 때, 화면에서의 리스트 칼럼과 엑셀 다운로드 시 보여줄 칼럼이 다를 때 필드가 1-2개 달랐지만 하나의 Dto로 사용했었습니다.
    * 각각의 목적에 따라 Dto를 만들어야합니다.
    * Dto는 용도와 레벨에 맞는 곳에 위치해야 합니다.
      * dto란 패키지를 만들고 그 안에 모든 dto를 두었습니다.
      * 하지만 어느 dto는 controller단까지, 어느 dto는 서비스 레벨까지 사용할 수 있습니다. 이런 용도와 레벨에 맞게 위치시켜야 합니다.
    * Payment Entity를 저장하기 위해 웹에서 값을 받는 PaymentRequestDto를 만들었습니다.
      * 여기서 금액을 저장하는 필드를 long타입으로 정했었는데, 이렇게 원시타입으로 하면 null값이 들어와도 0으로 들어오기 떄문에 문제를 감시할 수 없습니다. 사용자에게 입력받는 RequestDto는 레퍼런스타입으로 지정해야 합니다.
* 댓글
  * if 문이 많은 것 같아요. if문이 많다는 건 클래스가 두 가지 이상의 책임을 담당하고 있다는 신호일 수 있어서 주의해야해요~ 또한, if 문이 많아질수록 가독성이 떨어지며 단위테스트를 만들기가 어려워져요.
  * null 을 리턴하는 로직이 있는데 null 리턴하는 로직은 많아질수록 테스트 하기 어려워지고 결국 복잡성이 극에 달하면 client 코드에서는 null 을 체크하는 코드들로 도배될 수 있어요 가급적 null 은 리턴하지 마시고, null object 패턴을 사용하시는 것도 좋은 방법이 될것 같아요.
    * Optional 도 차선책이 될 수 있을 것 같지만 이것도 남용되면 좋지 않으니 주의하셔야 할것 같아요~

## 배민 병아리 개발자의 걸음마 한 발짝

* 운영에 대한 피드백
  * 비즈니스 로직에서 필요한 column은 별도로 만들어 처리하는 것이 좋다는 피드백
    * 비즈니스 로직에 JPA가 자동 생성해주는 column을 사용할 수는 없고
    * 새로운 column을 추가해 줌으로써 테스트 코드도 작성할 수 있게 되었음
  * @ManyToOne에서 optional = false를 사용하였는데,,
    * 해당 옵션은 연관관계를 맺는 객체가 null이어도 되는지, null이면 안되는지를 설정하는 옵션
  * item이라는 필드이 길이를 15로 제한
    * 문자열 길이 제한을 둔 이유는 딱히 없음
    * 하지만 이렇게 문자열 길이에 제한을 주게 되면 추후 길이 제한을 확장하거나 제거하게 되었을 때 수천만, 수억건의 데이터가 들어있는 테이블의 스키마를 변경해야 한다.
    * 데이터가 많을수록 적용되는 시간은 길다..
      * 그렇기 때문에 처음에 문자열 길이 제한은 넉넉하게 하는 것이 좋음
  * deletedDateTime은 2가지 역활을 하고 있었음
    * 해당 데이터가 삭제되었는지, 삭제되지 않았는지를 판별
    * 삭제된 시간을 기록
      * 이 2가지 역활은 서로 분리하는 것이 좋다.
    * 또한, null은 index로 사용할 수 없기 때문에 null이 들어가는 column으로 FLAG 역활을 하는 것보다 DeletedSales와 같은 별도의 테이블을 만들어 삭제된 Sales를 쌓는것이 좋다는 피드백을 들었음
* Controller
  * controller에 @RequestMapping와 메서드의 @RequestMapping에서 value값을 사용하게 되면..
    * IDE로 특정 API를 검색하기 어렵다.
      * 만약, /api/v2/user/signup이라는 full URL을 적어두면 IDE로 문자열 검색하여 해당 API를 처리하는 메서드를 바로 찾을 수 있다.
    * 또한, 모든 메소드들에 @RequestMapping이라는 어노테이션이 있어서 HTTP method를 한눈에 보기 어렵다.
    * 클래스 레벨의 @RequestMapping 제거
    * @PostMapping, @GetMapping등을 사용
  * API의 응답값으로 성공 여부에 대한 true / false를 반환
    * 그래서 return type은 boolean
    * 하지만 실패하는 경우에는 unchecked exception을 터뜨리도록 되어 있어 성공하면 무조건 HTTP status 200이 반환되는 상황
    * HTTP Status만으로 성공여부를 전달할 수 있어서 return type을 void로 처리
* 유지보수가 쉬운 코드
  * 

## 참고
* 코드리뷰 적응기(https://woowabros.github.io/experience/2019/02/28/pilot-project-settle.html)
* 병아리 개발자의 걸음마 한 발짝(https://woowabros.github.io/experience/2019/09/10/pilot-project.html)
* 정산지기를 위한 첫걸음(https://woowabros.github.io/experience/2020/03/02/pilot-project-wbluke.html)
