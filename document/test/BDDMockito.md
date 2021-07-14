## Mockito

Spring은 DI를 지원하여 의존성 주입을 신경쓰지않고 개발하는데, Test 하는 시점에서 문제가 발생한다.

단위 테스트를 작성할 경우 해당 객체에 대한 기능만 테스트하고 싶은데 의존성을 가지는 다른 객체에 의해 테스트 결과가 영향을 받을 수 있다는 것이다.

이렇게 의존을 가지는 객체를 우리가 원하는 동작만 하도록 만든 것이 Mock 객체이다.

Spring 에서 Mockito 를 사용하기 위해서는 `@ExtendWith(MockitoExtention.class`를 테스트상단에 지정해주면 된다. 그러면 `@Mock` 어노테이션을 통해 간단하게 Mock 객체를 만들어 사용할 수 있다.

Mock 객체에 원하는 동작을 미리 정하고 이를 기반으로 테스트할 수 있다.

## BDD(Behavior-Driven Development)

BDD는 행위 주도 개발을 말한다. 테스트 대상의 상태의 변화를 테스트하는 것이고, 시나리오를 기반으로 테스트 하는 패턴을 권장한다.

해당 패턴은 Given, When, Then 구조를 가진다.

이는 테스트 대상이 A 상태에서 출발하여(Given) 어떤 상태 변화를 가했을 때(When) 기대하는 상태로 완료되어햐 한다.(Then)

```java

// Mockito
@Test
void hasSkill_AlwaysTrue() {
	when(skills.hasSkill()).thenReturn(true);

	assertThat(person.hasSkill()).isTrue();
}

// Mockito를 사용하여 BDD 방식으로 구현
@Test
void hasSkill_AlwaysTrue() {
	// given
	when(skills.hasSkill()).thenReturn(true);

	// when
	boolean actual = person.hasSkill();

	// then
	assertThat(actual).isTrue();
}

// BDDMockito 사용
@Test
void hasSkill_AlwaysTrue() {
	// given
	given(skills.hasSkill()).willReturn(true);

	// when
	boolean actual = person.hasSkill();

	// then
	assertThat(actual).isTrue();
}
```

BDDMockito가 제공하는 기능과 Mockito가 제공하는 기능은 별반 다르지 않다. 단지 BDD라는 것을 테스트 코드에 도입할 때 Mockito가 가독성을 해치기 때문에 이를 해결하기 위한 기능은 같지만 이름만 다른 클래스라고 생각해도 된다.