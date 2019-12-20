# JPA relation

Relation에는 4가지 종류가 있습니다.
* OneToOne - 많이 사용되어지지않음
* ManyToOne - 주로 많이 사용
* OneToMany - 주로 많이 사용
* ManyToMany - 기능이 있지만, 사용되어서는 안됨(mapping Entitiy를 추가로 만들고, ManyToOne, OneToMany로 풀어내야한다.)

## 일대다 관계(OneToMany)

일대다 관계를 맺고있는 엔티티에는 외래키를 가지지않고, 해당 값을 참고하고 있는 것을 명시한다.
연관관계의 주인을 정하는 속성(`MappedBy`)를 사용하여 주인을 설정한다
(주인은 `MappedBy`를 사용하지않고 주인이 아닌 엔티티에서 사용한다.)
즉, 일대다를 가지는 엔티티는 MappedBy속성을 사용한다.
fetch의 기본값은 `lazy`이다.

## 다대일 관계(ManyToOne)

다대일 관계를 맺고있는 엔티티에 외래키를 가지고 있는것이 안정적이다.
* 외래키를 갖도록 하는 어노테이션은 `@JoinColumn`을 통해 외래키 필드를 지정한다.
* 해당하는 fetch는 기본값은 `eager`이다.

## 참고
