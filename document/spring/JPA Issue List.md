
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
