Proxy Pattern

Proxy는 대리자, 대변인 이라는 뜻이다.
대리자, 대리인은 다른 누군가를 대신해서 그 역활을 수행하는 존재이다.
프록시에게 어떤 일을 대신 시키는 것이다.
구체적으로 인터페이스를 사용하고 실행시킬 클래스에 대한 객체가 들어갈 자리에 대리자 객체를 대신 투입해 클라이언트 쪽에서 실제 실행시킬 클래스에 대한 객체를 통해 메서드를 호출하고 반환 값을 받는지, 대리자 객체를 통해 메서드를 호출하고 반환 값을 받는지 전혀 모르게 처리하는 것입니다.

프록시는 비서역활을 하는것 같다. 중요한 것은 흐름제어만 할 뿐 결과값을 조작하거나 변경시키면 안된다.

대리자는 실제 서비스와 같은 이름의 메서드를 구현한다. 이때 인터페이스를 사용한다.
대리자는 실제 서비스에 대한 참조 변수를 갖는다.
대리자는 실제 서비스의 같은 이름을 가진 메서드를 호출하고 그 값을 클라이언트에게 돌려준다.
대리자는 실제 서비스의 메서드 호출 전후에도 별도의 로직을 수행할 수 있다.

즉 정리하면, 원래 서비스의 메서드를 실행을 대신 해서 해주는것을 하는데, 하는 이유는 이 메서드의 실행 앞뒤로 별도의 로직을 수행할 수 있다.