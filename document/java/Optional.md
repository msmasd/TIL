# Java Optional

메서드가 반환할 결과값이 ‘없음’을 명백하게 표현할 필요가 있고, null을 반환하면 에러를 유발할 가능성이 높은 상황에서 메서드의 반환 타입으로 Optional을 사용하자는 것이 주된 목적이다. Optional 타입의 변수의 값은 절대 null이어서은 안되며, 항상 Optional 인스턴스를 가리켜야 한다.

## 안티패턴

### 1 isPresent()-get() 대신 orElese()/orElesGet()/orElseThrow()

```java
// anti
Optional<Member> member = ...;
if (member.isPresent()) {
  return member.get();
} else {
  return null;
}
// best
Optional<Member> member = ...;
return member.orElse(null);

//anti
Optinal<Member> member = ...;
if (member.isPresent()) {
  return member.get();
} else {
  throw new NoSuchElementException();
}

//best
Optional<Member> member = ...;
return member.orElseThrow() -> new NoSuchElementException();
```

### 2 orElse(new ...) 대신 orElseGet(() -> new ...)

orElse(...)에서 ...는 Optional에 값이 있든 없든 무조건 실행된다. 따라서 ...가 새로운 객체를 생성하거나 새로운 연산을 수행하는 경우에는 orElse() 대신 orElseGet()을 써야한다.

orElseGet(Supplier)에서 Supplier는 Optional에 값이 없을 때만 실행된다. 따라서 Optional에 값이 없을떄만 새 객체를 생성하거나 새 연산을 수행하므로 불필요한 오버헤드가 없다.

```java
// anti
Optional<Member> member = ...;
return member.orElse(new Member()); // member에 값이 있든 없든 new Member()는 무조건 실행

// best
Optional<Member> member = ...;
return member.orElseGet(new Member()); // member에 값이 없을때만 new Member() 실행
// best
Member EMPTY_MEMBER = new Member();
...
Optional<Member> member = ...;
return member.orElse(EMPTY_MEMBER); // 이미 생성했거나 계산된 값은 orElse()를 사용해도 무방하지만 위에 방법을 쓰는게 더 좋음
```

### 3 단지 값을 얻을 목적이라면 Optional 대신 null 비교

**Optional은 비싸다** 따라서 단순히 값 또는 null을 얻을 목적이라면 Optional 대신 null 비교를 쓰자.

```java
// anti
return Optional.ofNullable(status).orElse(READY);

// best
return status !== null ? status : READY;
```

### 4 Optional 대신 비어있는 컬렉션 반환

**Optional은 비싸다** 그리고 컬렉션은 null이 아니라 비어있는 컬렉션을 반환하는 것이 좋을 때가 많다. 따라서 컬렉션은 Optional로 감싸서 반환하지 말고 비어있는 컬렉션을 반환하자.

```java
// anti
List<Member> members = team.getMembers();
return Optional.ofNullable(member);

// best
List<Member> members = team.getMembers();
return members != null ? members : Collections.emptyList();

// 컬렉션을 반환하는 Spring Data JPA Repository 메서드는 null을 반환하지 않고 비어있는 컬렉션을 반환해주므로 Optional로 감싸서 반환할 필요가 없다.
// anti
public interface MemberRepository<Member, Long> extends JpaRepository {
  Optional<List<Member>> findAllByNameContaining(String part);
}

// best
public interface MemberRepository<Member, Long> extends JpaRepository {
  List<Member> findAllByNameContaining(String part);
}
```

### 5 Optional을 필드로 사용 금지

Optional은 필드에 사용할 목적으로 만들어지지 않았으며, Serializable을 구현하지 않았다. 따라서 Optional은 필드로 사용하지 말자.

```java
// anti
public class Member {
  private Long id;
  private String name;
  private Optional<String> email = Optional.empty();
}

//best
public class Member {
  private Long id;
  private String name;
  private String email;
}
```

### 6 Optional을 생성하거나 메서드 인자로 사용 금지

Optional을 생성자나 메서드 인자로 사용하면, 호출할 때마다 Optional을 생성해서 인자로 전달해 줘야 한다. 하지만 호출되는 쪽, 즉 api나 라이브러리 메서드에서는 인자가 Optional이든 아니든 null체크를 하는 것이 언제나 안전하다. 따라서 굳이 비싼 Optional을 인자로 사용하지 말고 호출 되는 쪽에 null 체크 책임을 남겨두는 것이 좋다.

```java
// anti
public class HRManager {
  public void increaseSalary(Optional<Member> member) {
    member.ifPresent(member -> member.increaseSalary(10));
  }
}

hrManager.increaseSalary(Optional.ofNullable(member));
// best
public class HRManager {
  public void increaseSalary(Member member) {
    if (member != null) {
      member.increaseSalary(10);
    }
  }
}
hrManager.increaseSalary(member);
```

### 7 Optional을 컬렉션의 원소로 사용 금지

컬렉션에는 많은 원소가 들어갈 수 있다. 따라서 비싼 Optional을 원소로 사용하지 말고 원소를 꺼낼때나 사용할 때 null 체크하는 것이 좋다. 특히 Map은 getOrDefault(), putIfAbsent(), computeIfAbsent(), computeIfPresent() 처럼 null 체크가 포함된 메서드를 제공하므로, Map의 원소로 Optional을 사용하지 말고 Map이 제공하는 메서드를 활용하는 것이 좋다.

```java
// anti
Map<String, Optional<String>> sports = new HashMap<>();
sports.put("100", Optional.of("BasketBall"));
sports.put("101", Optional.ofNullable(someOtherSports));
String basketBall = sports.get("100").orElse("BasketBall");
String unknown = sports.get("101").orElse("");

// best
Map<String, String> sports = new HashMap<>();
sports.put("100", Optional.of("BasketBall"));
sports.put("101", null);
String basketBall = sports.getOrDefault("100", "BasketBall");
String unknown = sports.computeIfAbsent("101", k -> "");
```

### 8 of(), ofNullable() 혼동주의

of(X) 은 X가 null이 아님이 확실할때만 사용해야 하며, X가 null이면 NullPointerException이 발생한다.
ofNullable(X)는 X가 null일 수도 있을 때만 사용해야 하며, X가 null이 아님이 확실하면 of(X)를 사용해야 한다.

```java
// anti
return Optional.of(member.getEmail()) // member의 email이 null이면 NPE 발생

// best
return Optional.ofNullable(member.getEmail());

// anti
return Optional.ofNullable("READY");

// best
return Optional.of("READY");
```

### 9 Optional<T> 대신 OptionalInt, OptionalLong, OptionalDouble

Optional에 담길 값이 int, long, double이라면 Boxing/Unboxing이 발생하는 Optional<Integer>, Optional<Long>, Optional<Double>을 사용하지 말고, OptionalInt, OptionalLong, OptionalDouble을 사용하자.

```java
// anti
Optional<Integer> count = Optional.of(38); // boxing 발생
for (int i=0; i < count.get(); i++) { ... } // unboxing 발생

// best
OptionalInt count = Optional.of(38);
for (int i=0; i < count.get(); i++) { ... }
```

## 참고

1. [참고 블로그](http://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/)
