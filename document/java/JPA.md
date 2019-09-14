# JPA

JPA(Java Persistence API)
JavaSE, EE를 위한 영속성(persistence) 관리와 ORM을 위한 표준 기술
Hibernate, OpenJPA, EclipseLink 등등 같은 구현체가 있고 이에 표준 인터페이스가 바로 JPA다.

왜 배우는지?

* 도메인 주도 개발이 가능하다.
* 애플리케이션의 코드가 SQL 데이터베이스 관련 코드에 잠식 당하는 것을 방지하고 도메인 기반의 프로그래밍으로 비즈니스 로직을 구현하는데 집중할 수 있습니다.
* 개발 생산성이 좋으며, 데이터베이스에 독립적인 프로그래밍이 가능하고, 타입 세이프한 쿼리 작성
* Persistent Context가 제공하는 캐시 기능으로 성능 최적화까지 가능

## 1 ORM

ORM(Object-Relational Mapping)
RDB테이블을 객체지향적으로 사용하기 위한 기술.
RDB 테이블은 객체지향적 특징(상속, 다형성, 레퍼런스, 오브젝트 등)이 없고 자바와 같은 언어로 접근하기 쉽지 않다.
때문에 ORM을 사용해 오브젝트와 RDB 사이에 존재하는 개념과 접근을 객체지향적으로 다루기 위한 기술

## 2 장점

* 객체지향적으로 데이터를 관리할 수 있기 때문에 비즈니스 로직에 집중할수 있고, 객체지향 개발이 가능하다
* 테이블 생성, 변경, 관리가 쉽다
* 로직을 쿼리에 집중하기 보다는 객체자체에 집중할 수 있다.
* 빠른 개발이 가능하다.

## 3 단점

* 어렵다. 장점을 더 극대화 하기 위해서 알아야 할게 많다.
* 잘 이해하고 사용하지 않으면 데이터 손실이 있을수 있디(persistence context)
* 성능상 문제가 있을 수 있다(이 문제 또한 잘 이해해야 해결이 가능하다.)

## 4 JPA 예시

```java
@Entity(name = "tbl_user")
public class User {
    @Id @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer userId;
    private String username;
    private String nickName;
    private String address;
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Order> orders;
}

@Entity(name = "tbl_order")
public class Order {
    @Id @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer orderId;
    private String orderName;
    private String note;
    private int price;
    @ManyToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL)
    @JoinColumn(name = "user_id")
    private User user;
}
```

## 5 JPA 구성

### 5.1 Entity(Domain)

* JPA에서 Entity는 하나의 테이블 객체를 표현한 것이라고 생각하면 된다.
* @Entity가 테이블 정보이며 변수가 필드가 되는것이다.

```java
@Entity(name = "tbl_user")
public class User {

    @Id @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer userId;
    private String username;
    private String nickName;
    private String address;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Order> orders;

    // getter, setter 영역

    public int totalPrice() {
        int totalPrice = 0;
        for (Order order : orders) {
            totalPrice += order.getPrice();
        }
        return totalPrice;
    }

    @Override
    public String toString() {
        return "User{" +
                "userId=" + userId +
                ", username='" + username + '\'' +
                ", nickName='" + nickName + '\'' +
                ", address='" + address + '\'' +
                ", orders=" + orders +
                '}';
    }
}


@Entity(name = "tbl_order")
public class Order {

    @Id @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer orderId;
    private String orderName;
    private String note;
    private int price;

    @ManyToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL)
    @JoinColumn(name = "user_id")
    private User user;

    public Order(String orderName, String note, int price, User user) {
        this.orderName = orderName;
        this.note = note;
        this.price = price;
        this.user = user;
    }

    // getter, setter 영역

    @Override
    public String toString() {
        return "Order{" +
                "orderId=" + orderId +
                ", orderName='" + orderName + '\'' +
                ", note='" + note +
                '}' + "\n";
    }
}
```
