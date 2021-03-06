xUnit Test Patterns의 저자인 제라드 메스자로스가 만든 용어로 테스트를 진행하기 어려운 경우 이를 대신해 테스트를 진행할 수 있도록 만들어주는 객체를 말한다.

> 영화 촬영 시 위험한 역활을 대신하는 스턴트 더블에서 비롯되었다.

예를 들어 우리가 데이터베이스로부터 조회한 값을 연산하는 로직을 구현했다고 하자. 해당 로직을 테스트하기 위해선 항상 데이터베이스의 영향을 받을 것이고, 이는 데이터베이스의 상태에 따라 다른 결과를 유발할 수도 있다.

이렇게 테스트하려는 객체와 연관된 객체를 사용하기가 어렵고 모호할 때 대신해 줄 수 있는 객체를 **테스트 더블** 이라 한다.

### 테스트 더블의 종류

테스트 더블은 크게 Dummy, Fake, Stub, Spy, Mock 으로 나눈다.

1. Dummy
    1. 가장 기본적인 테스트 더블이다.
    2. 인스턴스화 된 객체가 필요하지만 기능은 필요하지 않은 경우에 사용
    3. Dummy 객체의 메서드가 호출되었을 때 정상 동작은 보장하지 않는다.
    4. 객체는 전달되지만 사용되지 않는 객체이다.
2. Fake
    1. 복잡한 로직이나 객체 내부에서 필요로 하는 다른 외부 객체들의 동작을 단순화하여 구현한 객체이다.
    2. 동작의 구현을 가지고 있지만 실제 프로덕션에서 적합하지 않은 객체이다.
3. Stub
    1. Dummy 객체가 실제로 동작하는 것 처럼 보이게 만들어 놓은 객체이다.
    2. 인터페이스 또는 기본 클래스가 최소한으로 구현된 상태이다.
    3. 테스트에서 호출된 요청에 대해 미리 준비해둔 결과를 제공한다.
4. Spy
    1. Stub의 역활을 가지면서 호출된 내용에 대해 약간의 정보를 기록한다.
    2. 테스트 더블로 구현된 객체에 자기 자신이 호출 되었을 때 확인이 필요한 부분을 기록하도록 구현한다.
    3. 실제 객체처럼 동작시킬 수도 있고, 필요한 부분에 대해서는 Stub로 만들어서 동작을 지정할 수 있다.
5. Mock
    1. 호출에 대한 기대를 명세하고 내용에 따라 동작하도록 프로그래밍 된 객체이다.

### 참고

- [https://velog.io/@lxxjn0/Test-Double을-알아보자](https://velog.io/@lxxjn0/Test-Double%EC%9D%84-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)