
# JPA Issue List

## 1. toString에서 stackoverflow 발생 이슈

ManyToOne, OneToMany와 같이 양방향 연관관계를 가진 엔티티에서 lombok을 사용하여 toString을 선언하였을때,
ManyToOne, OneToMany로 있는 toSting들이 계속 호출되어서 stackoverflow를 발생시킨다.
oneToMany에 있는 필드를 toString에 exclude를 해주면 해결된다.
@toString(exclude = "field")

* 참고: https://lng1982.tistory.com/300

## 2. N+1 이슈

oneToMany를 포함하고 있는 엔티티에서 해당 값을 가져올려고 하면,
query가 oneToMany의 개수만큼 발생하는 이슈가 생긴다.(이래서 N+1이슈)

해당 이슈를 해결하기 위해서는 쿼리에서 조인을 한다고 말을 해주던가(Join Fetch, @EntityGraph)
-> 하지만 이렇게 하면 outter join이기때문에 oneToMany의 필드가 중복된다.

해결방안은 2가지
* 일대다 필드의 타입을 `Set`으로 선언
  * `Set`은 중복읠 허용하지 않는 자료구조이기 때문에 중복등록 불가
  * `Set`은 순서가 보장되지 않기에 LinkedHashSet을 사용하여 순서를 보장
* `distinct`를 사용하여 중복을 제거
  * 여기서는 List
  * 해당 부분은 `@Query`에서 적용하는 것이니, joinFetch, @EntityGraph 모두 동일
* 추가로, `@NamedEntityGraphs`로 예시를 많이 든다
  * Entity에 관련해서 모든 설정 코드를 추가해야하는데, 개인적으론 Entity가 해야하는 책임에 포함되지 않는다고 생각.
  * A 로직에서 Fetch전략을 어떻게 가져가야 한다는 것은 해당 로직의 책임이지, Entity의 책임이 아니라고 생각

* 참고: https://jojoldu.tistory.com/165

## 3. ManyToOne에서 one을 추가하는 방법

다대일(ManyToOne)을 가진 엔티티는 해당 다대일의 값을 setter하는 부분을 재정의해서 해야한다.
```java
@Entity
@Data
public class Account {
    ..
    @OneToMany(mappedBy = "account")
    public Test test;
}

@Entitiy
@Data
public Class Test {
    @ManyToOne
    @JoinColume(name = "account_id")
    public List<Account> account = new ArrayList<>();

    // 이 setter를 재정의한다.
    // 현재 있는 test객체에 account 값 넣어주고
    // account로 들어온 값에 현재 test객체가 없으면 add해준다.
    // 즉, test객체와 account객체 모두에 값을 세팅해준다.
    public void setAccount(Account account) {
        this.account = account;
        if (!account.getTest().contains(this)) {
            account.getTest().add(this);
        }
    }
}
```
위와 같이 하지않으면, 한 트랜잭션 안에서 account를 다시 find해 test객체를 비교하였을때, add한 test객체가 없는것을 확인할 수 있다.
위와 같은 이슈를 해결하기 위해서는 setter를 재정의하여 일대다 객체에도 세팅을 해주면 된다.

## 4. 관계 매핑시, 여러 키를 외래키로 등록할 경우(JoinColumns)

```java
@Entity
@Data
public class Geo {
    @Id @GeneratedValue
    private Long id;
    ...
    private String city;
    private String state;
    ..
}

@Entity
@Data
public class Account {
    @Id @GeneratedValue
    private Long id;

    ...

    // Geo를 매핑하는데 id값이 아닌 city와 state의 값으로 매핑하고싶다! 그리고 해당 값을 디비에는 account_city, account_state라고 이름을 변경해 저장하고싶다면!
    @ManyToOne
    @JoinColumns({
        @JoinColumn(name = "account_state", referencedColumnName = "state") // referencedColumnName은 대상이 되는 실제 객체의 멤버변수명(디비명 아니다!), name은 기존과 동일하게 내가 디비에 저장할 필드명
        @JoinColumn(name = "account_city", referencedColumnName = "city")
    })
    private Geo geo;
    ..
}
```

위처럼 하나의 엔티티에서 `@ManyToOne`을 할때 여러개의 외래키 참조할려면 `@JoinColumns`를 통해 `@JoinColumn`을 묶어준다.
* `@JoinColumn`은 어떤 멤버변수를 외래키로 정해주는 어노테이션

## 5. insert시 필드정보를 못가져오는 이슈

```java
@Entity
public class Article {
    @Id @GeneratedValue
    private Long Id;

    @Convert(converter = EnumTypeConverter.class)
    private EnumType type; // enum으로 되어있고, 해당 EnumType은 converter처리가 되어있다. toDatabaseColumn, toEntityAttribute -> 여기서 값이 null로 들어와있으면 에러가 발생

    private String content;
    private String title;

    @ManyToOne
    private Headline headline;

    public void setHeadline(Headline headline) {
        this.headline = headline;
        if (!headline.getArticles().contains(this)) {
            headline.getArticles().add(this);
        }
    }
}

public class Service {
    ..
    @Transactional
    public Article createArticle(ArticleCreate request) {
        Article article = new Article();
        article.setContent(request.getContent());
        article.setTitle(request.getTitle());

        // 여기서 getHeadline에서 트랜잭션이 완료가되어 managed에 있는 것들이 commit이 되는 상황이 생기는거 같다. 아니면 특정 상황에서 commit 혹은 flush가 발생한다.
        // flush가 발생하면 article이 insert가 되어야 하는데.. article은 EnumType이 null이 되면 안되는 상황이다.
        // 아래 코드에서 type을 set하는 코드가 있지만 해당 라인에서는 type이 null이기 때문에 EnumType에서 toDatabaseColumn부분에서 에러가 발생한다.
        // 이러면 해당 코드가 에러가 발생하여 동작하지 않는다..
        // article에 대해서 enum이나 데이터가 반드시 필요한 부분에서는
        // 1. 이런 부분이 없도록 하는것이 베스트
        // 2. 이런 부분이 있다면, commit이나 flush가 발생될것으로 보이는 코드보다 더 먼저 set해주기.
        HeadLine headline = headlineService.getHeadline(request.getHeadlineId());
        article.setHeadline(headline);

        article.setType(EnumType.SPORT);

        return articleRepository.save(article);
    }
    ..
}
```

* jpa가 commit이 되는 경우에 해당 insert가 발생을 할 수 있는데, 그때에 enumType에 대한 값들이 제대로 들어가있지않으면 에러가 발생해버린다.
* 하지만 enumType같은 데이터 컨버터가 없다면 commit이 된 후에, 아래에 해당 값들을 set하는 로직이 있다면 최종적으로, 해당 로직이 포함된 insert가 발생하게 된다.

* Id가 GenaratedValue를 사용하였을때는, 해당 엔티티의 객체가 엔티티관리자에 의해 관리되는 상태(영속상태)가 되었을때, 해당 상황만 예외적으로 바로 insert가 발생한다.
  * 그래서.. insert코드가 바로 날라가는듯 하다.. getHeadline을 가져와서 `article.setHeadline(headline);`부분에서 article이 영속상태로 관리가 되는 상황인거 같은데.. 이때 insert가 날라가는듯 하다.ㄴㄴ

## 6. JPA CumtomImpl NoSuchBeanDefinitionException

해당 이슈는 `QuerydslRepositorySupport`에 있는 기본 생성자를 Override할때, 자동생성되어지는 코드에서
```java
public CodeLogRepositoryCustomImpl(Class<?> domainClass) {
    super(domainClass);
}
// 이 코드를
public CodeLogRepositoryCustomImpl() {
    // 해당 Entity의 클래스 이름
    super(ClassName.class);
}
// 위와 같이 수정해야 에러가 발생하지않는다.
```
