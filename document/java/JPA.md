# Java JPA

JPA(Java Persistent API)

JPA는 여러 ORM 전문가가 참여한 EJB 3.0 스펙 작업에서 기존 EJB ORM이던 Entity Bean을 JPA라고 바꾸고 JavaSE, JavaEE를 위한 영속성(persistence)관리와 ORM을 위한 표준 기술이다.
JPA는 ORM 표준 기술로 Hibernate, OpenJPA, EclipseLink와 같은 구현체가 있고 이에 표준 인터페이스가 바로 JPA이다.

ORM(Object Relation Mapping)은 RDB 테이블을 객체지향적으로 사용하기 위한 기술이다. RDB 테이블은 객체지향적 특징(상속, 다형성, 레퍼런스, 오브젝트 등)이 없고 자바와 같은 언어로 접근하기 쉽지 않다. 때문에 ORM을 사용해 오브젝트와 RDB 사이에 존재하는 개념과 접근을 객체지향적으로 다루기 위한 기술이다.

## 장점

* 객체지향적으로 데이터를 관리할 수 있기 때문에 비즈니스 로직에 집중 할 수 있으며, 객체지향 개발이 가능하다.
* 테이블 생성, 변경, 관리가 쉽다(JPA를 잘 이해하고 있는 경우)
* 로직을 쿼리에 집중하기 보다는 객체자체에 집중 할 수 있다.
* 빠른 개발이 가능하다.

## 단점

* 어렵다. 장점을 더 극대화 하기위해서 알아야 할게 많다.
* 잘 이해하고 사용하지 않으면 데이터 손실이 있을 수 있다.(persistence context)
* 성능상 문제가 있을 수 있다. (이 문제 또한 잘 이해해야 해결이 가능하다.)

## 왜 ORM인가?

한국은 xBatis를 사용하는데, 쿼리 자체에 집중하고 비즈니스 로직을 쿼리에 의존하게 되면 객체지향의 장점을 놓칠 수 있는 가능성이 크다.

User와 Order테이블이 아래와 같이 있다고 가정해보자.

```java
User {
    int userId;
    String username;
    String nickName;
    String address;
}

Order {
    int orderId;
    int userId;
    String orderName;
    String note;
}
```

이런 상황에서 DTO 또는 VO 클래스를 만드는데 User가 자신이 생성한 Order 데이터를 가져오기 위해서는
```sql
select * from tbl_user u join tbl_order o on u.user_id = o.user_id
where u.user_id = #{userId}
or
select * from tbl_order where user_id = #{userId}
```

여기서 주의해서 봐야할 부분은 Order클래스에 userId 변수이다.
이 변수는 order를 생성한 User의 id를 가지고 있다.즉, 테이블 관점으로 본다면 userId변수는 fk가 되는 것이다.
User 객체가 아니라 User의 pk만 가지고 있는 것이다.
그럼, ORM에서는 객체를 어떻게 구성할까?

```java
@Entity(name = "tbl_user")
public class User {
    @Id @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer userId;
    private String username;
    private String nickName;
    private String address;
    @OneToMany(mappedBy = "user", cascaed = CascadeType.ALL)
    private List<Order> orders;
}

@Entity(name = "tbl_order")
public class Order {
    @Id @GeneratedValue(stratage = GenerationType.AUTO)
    private Integer orderId;
    private String orderName;
    private String note;
    private int price;
    @ManyToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL)
    @JoinColumn(name = "user_id")
    private User user;
}
```
User 객체는 OrderList 객체를, Order 객체는 User 객체를 가지고 있다.
테이블 관점으로 보나, 객체 관점으로 보나 pk, fk도 만족을 하며, 객체지향적으로 객체를 만든 것이다.
심지어 데이터를 조작하기 위한 쿼리 자체도 신경쓰지 않는다. 쿼리 자체를 신경쓰지 않는다는 뜻은 실제 User 객체나 Order객체에 테이블에서 조회해서 만드는 과정에 있어 위 예제와 같이 쿼리를 작성해서 만들지 않겠다는 뜻이다.(내부적으로는 쿼리가 실행되고 실제로 우리는 그 쿼리가 어떻게 만들어지고 실행되는지 정확히 인지하고 있어야 정확한 JPA 사용이 가능하다.) 우리가 쿼리를 작성하지 않는다는 뜻이지 쿼리가 실제로 실행 안된다는 뜻은 아니다.

이렇게 객체지향적인 객체가 생성되면 우린 비즈니스 로직 자체로 쿼리에 집중하지 않고 자바 코드 자체에 집중 할 수 있다는 것이다.

## Entity

JPA에서 Entity는 하나의 테이블 객체를 표현한 것이라고 생각하면 된다.
@Entity가 테이블 정보이며 변수가 필드가 되는것이다.

### Entity Lifecycle

* new(비 영속 객체)
  * Entity 객체가 DB에 반영되지 않았고, Managed 상태가 아닌 상태를 말한다.
  * 이 상태는 new 키워드를 사용해 생성한 Entity 객체를 말하고 영속화되지 않는다.
* Managed(영속 객체)
  * Entity 객체가 영속 객체가 된 상황은 크게 2가지 있다.
    * New상태에서 persist메소드를 이용해 저장한 경우
    * DB테이블에 저장돼 있는 데이터를 find메소드 또는 query를 사용해 조회한 경우이다.
  * 이 상태는 Persistence Context가 관리하는 상태이며, 해당 객체를 수정했는지(자동 변경 감지) 알아낼 수 있다.
* Removed(삭제 객체)
  * Managed 상태인 객체를 remove 메소드를 이용해 삭제한 경우에 Removed상태에 해당한다.
  * 작업 단위가 종료되는 시점에 실제로 DB 테이블에 삭제가 동기화 된다.
  * 이 상태는 객체는 작업 단위가 종료되는 동시에 DB에서 삭제되므로 재사용하면 안된다.
* Detached(준 영속 객체)
  * 트랜잭션이 commit되었거나, clear, flush 메소드가 실행된 경우 managed상태의 객체는 모두 Detached 상태가 된다.
  * 이 상태는 더이상 DB와 동기화를 보장하지 않는다.
  * 다시 Managed 상태로 만들기 위한 merge 메소드가 존재한다.

### Persistence Context(영속성 컨텍스트)

영속성 컨텍스트는 단순히 캐시를 처리하기 위한 개념은 아니다. 이는 실제로 우리가 API를 통해 관리하거나 눈에 보이는 것은 아니다.
JPA에서는 EntityManager가 영속성 컨텍스트를 갖는다.

* 자동 변경 감지
  * Managed 상태의 객체가 영속성 컨텍스트에서 관리된다.
  * 자동변경감지란, 영속 상태의 객체의 속성이 변경됨을 자동으로 감지하고 영속성 컨텍스트에서 관리되는 객체와 자동 동기화된다.
  * 동기화된 객체(영속 객체)는 영속성 컨텍스트의 작업 단위가 종료되는 시점에 DB와 동기화 시킨다.
  * 즉, 영속성 컨텍스트에서 관리되는 영속 객체는 바로 DB와 동기화 되는 것이 아니라 작업 단위가 종료될 때만 동기화된다. (가능한 늦게 DB로 상태변경을 전파한다)
* 캐시
  * 영속성 컨텍스트가 작업 단위에서 다루는 모든 객체(영속 객체)는 영속성 컨텍스트에서 기억한다.
  * 예를들어, key를 기준으로 Entity 객체를 조회한 경우, DB 데이터를 조회하기 전에 영속성 컨텍스트에서 영속 객체를 먼저 조회한다.
  * 이 때, key에 해당하는 영속 객체가 존재하지 않는 경우에만 DB에서 데이터를 조회한다.
  * 이를 반복 가능한 읽기라고 하며, DB를 조회하지 않고 영속성 컨텍스트에서 영속 객체를 가져오기 때문에 성능상 많은 이점을 준다.
* 객체 동일성
  * 자바에서는 "==" 연산자를 사용해 객체 동일성을 체크한다.
  * JPA에서 동일성은 자바와 DB데이터에서 모두 일치하는 경우를 동일성이라고 판단한다.
  * 즉, 자바에서 "=="연산자를 통한 동일성도 일치해야하며, DB에서는 key가 일치한 경우에만 동일성이 일치한다고 판단한다.
  * key가 같아 자바 동일성은 해당되지만 작업 단위가 다른 경우에는 불일치하다는 뜻이다.

## annotation

* @Entity
  * JPA를 사용해서 테이블과 매핑할 클래스를 지정한다.
  * name: 엔티티 이름을 지정(설정하지 않으면 클래스 이름을 그대로 사용)
* @Table
  * 엔티티와 매핑할 테이블을 지정한다. 생략하면 매핑한 엔티티 이름을 테이블 이름으로 사용.
* @Id
  * 테이블에서 Identity키로 사용할 필드를 지정
* @Column
  * name: 매핑할 Column 이름
  * nullable: null값의 허용 여부를 결정
  * unique: 한 컬럼에 간단히 유니크 제약조건을 걸때 사용
  * columnDefinition: DB 컬럼 정보를 직접 줄 수 있다.
  * length: 문자 길이 제약조건, String타입에만 사용
  * percision, scale: BigDecimal 타입에서 사용한다. precision은 소수점을 포함한 전체 자릿수, scale은 소수의 자릿수
* @Enumerated
  * 자바의 enum 타입을 컬럼에 매핑할때 사용한다.
  * value
    * EnumType.ORDINAL: enum 순서를 값으로 DB에 저장
    * EnumType.STRING: enum 이름을 값으로 DB에 저장
* @Temporal
  * 날짜 타입(java.util.Data, java.util.Calendar)을 매핑할 때 사용
  * value
    * TemporalType.DATE: 날짜, 데이터베이스 date타입과 매핑
    * TemporalType.TIME: 시간, 데이터베이스 time타입과 매핑
    * TemporalType.TIMESTAMP: 날짜와 시간, 데이터베이스 timestamp 타입과 매핑
* @Lob
  * 데이터베이스 BLOB, CLOB, TEXT 타입과 매핑
* @Transient
  * 지정된 필드는 매핑하지 않는다. 객체에 임시로 어떤 값을 보관하고 싶을때 사용
* @GeneratedValue
  * strategy: 원하는 키 생성 전략을 선택
    * IDENTITY: 기본키 생성을 데이터베이스에 위임하는 방법(데이터베이스에 의존적) / 주로 MySQL, PostgresSQL, SQL Server, DB2에서 사용
    * SEQUENCE: 데이터베이스 시퀀스를 사용해서 기본 키를 할당하는 방법(데이터베이스에 의존적) / 주로 시퀀스를 지원하는 Oracle, PostgresSQL, DB2, H2에서 사용 / @SequenceGenerator를 사용해여 시퀀스 생성기를 등록하고, 실제 데이터베이스의 생성될 시퀀스이름을 지정해줘야 한다.
    * TABLE: 키 생성 테이블을 사용하는 방법 / 키 생성 전용 테이블을 하나 만들고 여기에 이름과 값으로 사용할 칼럼을 만드는 방법입니다. / 테이블을 사용하므로, 데이터베이스 벤더에 상관없이 모든 데이터베이스에 적용이 가능합니다.
    * AUTO: 데이터베이스 벤더에 의존하지 않고, 데이터베이스는 기본키를 할당하는 방법 / 데이터베이스에 따라서 IDENTITY, SEQUENCE, TABLE 방법 중 하나를 자동으로 선택해주는 방법 / 예를들어, Oracle일 경우 SEQUENCE를 자동으로 선택해서 사용. 따라서 데이터베이스를 변경해도 코드를 수정할 필요가 없습니다.
* @Mapping
  * @OneToMany
  * @ManyToOne
  * @ManyToMany
    * optional: false로 설정하면 연관된 엔티티가 항상 있어야 한다.
    * fetch: 글로벌 패치 전략을 설정한다.
    * cascade: 영속성 전이 기능을 사용한다.
    * mappedBy: 양방향 관계에서 주체가 되는 쪽(Many쪽, 외래키가 있는 쪽)을 정의
* @JoinColumn
  * 테이블 연관관계를 매핑할 때 foreign key를 매핑할 때 사용
  * name: 매핑할 foreign key 이름
  * referencedColumnName: foreign key가 참조하는 대상 테이블의 컬럼명
  * foreignKey: foreign key 제약조건을 직접 지정할 수 있다. 이 속성을 테이블을 생성할 때만 사용가능
* @JoinTable
  * joinColumns
  * inverseJoinColumns
* @MappedSuperclass
* @Convert
  * converter
* @EqualsAndHashCode
  * include
* @ToString
  * exclude



## 참고

* https://blog.woniper.net/255
