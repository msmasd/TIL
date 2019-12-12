# mapStruct

* MapStruct is a code generator that greatly simplifies the implementation of mappings between Java bean types based on a convention over configuration approach.
* The generated mapping code uses plain method invocations and thus is fast, type-safe and easy to understand.

즉, 자바 빈들간의 매핑을 가장 간단히 구현해주는 코드 생성기이다. 사용하기 쉽고, 이해하기 쉽다.
그리고 `Mapper`를 만들면, 빌드시 Mapper 인터페이스의 구현체가 만들어지는데, mapper가 어떻게 구현되었는지 확인도 가능하다.

## 사용법

```java
@Mapper(componentModel = "spring") // 3
public interface CarMapper {
    CarMapper INSTANCE = Mappers.getMapper( CarMapper.class );

    @Mapping(source = "numberOfSeats", target = "seatCount") // 1
    @Mapping(source = "numberOfDesks", target = "deskCount")
    CarDto toDto(Car car);

    @Mapping(target = "id", ignore = true) // 2
    Car toEntity(CarDto dto);
}
```

* 기본적으로 인자명이 같으면 `@Mapping` 어노테이션을 사용하지않아도 자동으로 매핑해준다.
  * `@Mapping`을 사용하는 이유는 필드명이 다른것이 있으면 convert해주기 위해서 사용된다.
  * `@Mapping`은 여러개 사용해도 무관하다.
* 1번은 인자로 있는 `Car`의 멤버변수인 `numberOfSeats`를 `CarDto`의 멤버변수인 `seatCount`에 매핑하는 것이다.
* 2번은 `CarDto`의 멤버변수인 `id` 멤버변수는 `Car`에 멤버변수가 있더라도 `ignore` 즉, 필드 매핑을 안하는 것이다.
* 3번은 단순 `@Mapper`만 사용해도 Mapper가 만들어지지만, `componentModel`은 어떤 빈을 매핑할것인지 지정하는 것이다.
  * default는 `no component model`입니다.

#### null인 데이터는 mapping하지 않는 방법

인터페이스에 선언되어지는 @Mapper나 메서드에 선언되는 @Mapping의 속성중에는 nullValueCheckStrategy이라는 속성이 있다.
해당 속성은 source의 null을 어떻게 처리하여 target에 mapping하는지에 대한 전략을 물어보는데,
* NullValueCheckStrategy.ALWAYS는 source가 null인 값은 target에 null을 넣지않고 기존에 있던 값을 사용한다. `if(source.getValue() =!null) {
target.setValue(source.getValue());
`

* ON_IMPLICIT_CONVERSION는 디폴트값인데 null체크 없이 그냥 바로 set한다. 그래서 값이 없으면 null로 덮어씌어진다

## 참고

* 기본사용법: https://mapstruct.org/documentation/stable/reference/html/#basic-mappings
* 내용 참고: https://joont92.github.io/etc/MapStruct/
