# Java Lombok

Lombok은 자바 컴파일 시점에서 특정 어노테이션으로 해당 코드를 추가할 수 있는 라이브러리입니다.
이를 통해 코드가 간결해지고 가독성 및 유지보수에 많은 도움을 줍니다. 하지만, 편리한 만큼 잘못 사용하기 쉬운 것이 Lombok입니다.

## Annotations

### 1. @Data

다재다능한 어노테이션이다.
해당 어노테이션을 사용하면 @Getter, @Setter, @RequiredArgsConstructor, @ToString, @EqualsAndHashCode 어노테이션을 다 사용한것과 동일하다.

#### 속성

해당 어노테이션에는 속성(staticConstructor)이 하나 있다.
해당 속성은 말 그대로 static한 생성자를 만들어주는 것이다.

### 2. xxxxArgsConstructor

생성자를 생성해주는 어노테이션이다.
이런 어노테이션은 3가지가 존재한다.

* @NoArgsConstructor: 디폴트 생성자를 생성
* @AllArgsConstructor: 모든 필드의 생성자를 생성
* @RequiredArgsConstructor: 필수 생성자를 생성

#### 속성

* staticName: static한 생성자를 생성
* access: 접근제한을 할 수 있다. (PUBLIC, MODULE, PROTECTED, PACKAGE, PRIVATE)
* onConstructor: 생성자에 어노테이션을 작성할 수 있다.

```java
@RequiredArgsConstructor(staticName = "of", onConstructor = @__(@Inject))
public class ConstructorObject {
  private final Long id;
  private String name;
}
// 생성
class ConstructorObject {
  private final Long id;
  private String name;

  @Inject
  private ConstructorObject(Long id) {
    this.id = id;
  }
  public static ConstructorObject of(Long id) {
    return new ConstructorObject(id);
  }
}
```

### 3. @Getter와 @Setter

getter와 setter를 생성해준다.
클래스 레벨에도 사용가능하며, 필드 레벨에도 사용가능하다.

#### 속성

* 공통
  * value: 접근을 제한
  * onMethod: 해당 메서드에 어노테이션을 작성
* 개별
  * lazy(getter만): 필드의 값을 지연시킨다는 뜻
    * lazy가 true일때는 무조건 final필드이어야한다 / false인경우에는 객체를 생성할 때, 해당 객체를 생성하는 함수를 실행하지 않고, 실제 객체 호출인 getXX()를 하였을때, 객체를 생성하는 메서드를 그때 실행한다.
  * onParam(setter만): 파라미터의 어노테이션을 작성

```java
public class GetSetObject {
  
  @Getter(value = AccessLevel.PACKAGE, onMethod = @__({@NonNull, @Id}))
  private Long id;
}
// 생성
public class GetSetObject {
  private Long id;

  @Id
  @NonNull
  Long getId() {
    return id;
  }
}
```

### 4. @EqualsAndHashCode 와 @ToString

@EqualsAndHashCode는 이름 그대로 hashcode와 equals를 생성해주는 어노테이션.
@ToString도 동일하게 toString() 메서드를 생성해주는 어노테이션이다.

#### 속성

* 공통
  * exclude: 제외시킬 변수명
  * of: 포함시킬 변수명
  * callSuper: 상위 클래스의 호출여부를 묻는 속성
  * donotUseGetters: getter사용여부
* 개별
  * onParam(EqualsAndHashCode만): 파라미터의 어노테이션 작성
  * includeFileNames(ToStirng만): toString의 필드명을 출력할지 않할지의 여부

## 참고

1. http://wonwoo.ml/index.php/post/1607

