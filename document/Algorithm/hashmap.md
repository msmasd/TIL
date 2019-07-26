# java hashMap은 어떻게 동작하는가?

HashMap은 Java Collection Framework에 속한 구현체 클래스입니다. Java Collections Framework는 1998년 12월에 발표한 Java2에서 정식으로 선보였습니다. Map 인터페이스 자체는 Java5에서 Generic이 적용된 것 외에 처음 선보인 이후 변화가 없지만, HashMap 구현체는 성능을 향상시키기 위해 지속적으로 변화해 왔습니다.

이 글에서는 어떤 방식으로 HashMap 구현체의 성능을 향상시켰는지 소개합니다. 구체적인 내용은 Amortized Constant Time을 위하여 어떻게 해시 충돌(hash collision)가능성을 줄이고 있는가에 대한 것입니다.

## HashMap과 HashTable

HashMap과 HashTable은 Java의 API 이름입니다. HashTable이란 JDK 1.0부터 있던 Java의 API이고, HashMap은 Java2에서 처음 선보인 Java Collections Framework에 속한 API입니다.
HashTable 또한 Map 인터페이스를 구현하고 있기 때문에 HashMap과 HashTable이 제공하는 기능은 같다.

다만 HashMap은 보조 해시 함수(Additional Hash Function)를 사용하기 때문에 보조 해시 함수를 사용하지 않는 HashTable에 비하여 해시 충돌이 덜 발생할 수 있어 상대적으로 성능상 이점이 있다. 보조 해시 함수가 아니더라도, HashTable 구현에는 거의 변화가 없는 반면, HashMap은 지속적으로 개선되고 있다. HashTable의 현재 가치는 JRE 1.0, JRE 1.1 환경을 대상으로 구현한 Java 애플리케이션이 잘 동작할 수 있도록 하위 호환성을 제공하는 것에 있기 떄문에, 이 둘 사이에 성능과 기능을 비교하는 것은 의미가 없다.

HashMap과 HashTable을 정의한다면, **키에 대한 해시 값을 사용하여 값을 저장하고 조회하며, 키-값 쌍의 개수에 따라 동적으로 크기가 증가하는 associate array**라고 할 수 있다.
associate array를 지칭하는 다른 용어는 Map, Dictionary, Symbol Table 등이다.

associate array를 지칭하기 위하여 HashTable에서는 Dictionary라는 이름을 사용하고, HashMap에서는 그 명칭이 그대로 말하듯이 Map이라는 용어를 사용한다.

map(또는 mapping)은 원래 수학 함수에서의 대응 관계를 지칭하는 용어로, 경우에 따라서는 함수 자체를 의미하기도 한다. 즉 HashMap이란 이름에서 알 수 있듯이, HashMap은 키 집합인 정의역과 값 집합인 공역의 대응에 해시 함수를 이용한다. (키를 통하여 해시 함수를 통하면 값이 나온다)

## 해시 분포와 해시 충돌

동일하지 않은 어떤 객체 X와 Y가 있을 때, 즉 X.equals(Y)가 false일 때, X.hashCode() != Y.hashCode()가 같지 않다면, 이때 사용하는 해시 함수는 완전한 해시 함수(perfect hash functions)라고 한다.

Boolean같이 서로 구별되는 객체의 종류가 적거나, Integer, Long, Double 같은 Number객체는 객체가 나타내려는 값 자체를 해시 값으로 사용할 수 있기 때문에 완전한 해시 함수 대상으로 삼을 수 있다. 하지만 String이나 POJO에 대하여 완전한 해시 함수를 제작하는 것은 사실상 불가능 하다.

적은 연산만으로 빠르게 동작할 수 있는 완전한 해시 함수가 있다고 하더라도, 그것을 HashMap에서 사용할 수 있는 것은 아니다. HashMap은 기본적으로 각 객체의ㅏ hashCode() 메서드가 반환하는 값을 사용하는 데, 결과 자료형은 int다. 32비트 정수 자료형으로는 완전한 자료 해시 함수를 만들 수 없다. 논리적으로 생성 가능한 객체의 수가 2^32보다 많을 수 있기 때문이며, 또한 모든 HashMap 객체에서 O(1)을 보장하기 위해 랜덤 접근이 가능하게 하려면 원소가 2^32인 배열을 모든 HashMap이 가지고 있어야 하기 때문이다.

따라서 HashMap을 비롯한 많은 해시 함수를 이용하는 associative array 구현체에는 메모리를 절약하기 위하여 실제 해시 함수의 표현 정수 범위 N보다 작은 M개의 원소가 있는 배열만을 사용한다. 따라서 다음과 같이 객체에 대한 해시 코드의 나머지 값을 해시 버킷 인덱스 값으로 사용한다.

`int index = X.hashCode() % M; // 해시를 사용하는 associative array 구현체에서 저장/조회할 해시 버킷을 계산하는 방법`

이 코드와 같은 방식을 사용하면, 서로 다른 해시 코드를 가지는 서로 다른 객체가 1/M의 확률로 같은 해시 버킷을 사용하게 된다. 이는 해시 함수가 얼마나 해시 충돌을 회피하도록 잘 구현되었느냐에 상관없이 발생할 수 있는 또 다른 종류의 해시 충돌이다. 이렇게 해시 충돌이 발생하더라도 키-값 쌍 데이터를 잘 저장하고 조회할 수 있게 하는 방식에는 대표적으로 두가지가 있다. Open Addressing이고, 다른것은 Separate Chaining이다.

Open Addressing은 데이터를 삽입하려는 해시 버킷이 이미 사용중인 경우 다른 해시 버킷에 해당 데이터를 삽입하는 방식이다. 데이터를 저장/조회할 해시 버킷을 찾을 때에는 Linear Probing, Quadratic Probing등의 방법을 사용한다.

Separate Chaining에서 각 배열의 인자는 인덱스가 같은 해시 버킷을 연결한 링크드 리스트의 첫 부분(head)이다.

둘 모두 Worst Case O(M)이다. 하지만 Open Addressing은 연속된 공간에 데이터를 저장하기 때문에 Separate Chaining에 비하여 캐시 효율이 높다. 따라서 데이터 개수가 충분히 적다면 Open Addressing이 더 좋다. 하지만 배열의 크기가 커질수록 캐시 효율이라는 Open Addressing의 장점은 사라진다. 배열의 크기가 커지면, L1, L2 캐시 적중률(hit ratio)이 낮아지기 때문이다.

Java HashMap에서 사용하는 방식은 Separate Chaining이다. Open Addressing은 데이터 삭제시 처리가 효율적이기 어렵다. HashMap에 저장된 키-값 쌍 개수가 일정 개수 이상 많아지면, 일반적으로 Open Addressing은 느리다. Open Addressing의 경우 해시 커빗을 채운 밀도가 높아질수록 Worst Case 발생 빈도가 더 높아진다. 반면 Separate Chaining의 경우 해시 충돌이 잘 발생하지 않도록 '조정'할 수 있다면 Worst Case 또는 Worst Case에 가까운 일이 발생하는 것을 줄일 수 있다.

해시 버킷 동적 확장

HashMap은 키-값 쌍 데이터 개수가 일정 개수 이상이 되면, 해시 버킷의 개수를 두 배로 늘린다. 해시 버킷 개수를 늘리면, 해시 충돌로 인한 성능 손실 문제를 어느정도 해결할 수 있다.

해시 버킷 개수의 기본값은 16이고, 데이터의 개수가 임계점에 이를 때마다 해시 버킷 개수의 크기를 두 배씩 증가시킨다. 버킷의 최대 개수는 2^30개다. HashMap 생성자의 인자로 초기 해시 버킷 개수를 지정할 수 있으므로, 해당 HashMap 객체에 저장될 데이터의 개수가 어느 정도인지 예측 가능한 경우에는 이를 생성자의 인자로 지정하면 불필요하게 Separate Chaining을 재구성하지 않게 할 수 있다.

참고 [Java HashMap은 어떻게 동작하는가?](https://d2.naver.com/helloworld/831311)

