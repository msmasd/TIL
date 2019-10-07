# javascript prototype

javascript는 class개념이 없지만, 대신 프로토타입이라는 것이 존재한다.
자바스크립트는 프로토타입 기반 언어라고 불린다.
클래스가 없으니, 상속기능도 없다. 그래서 프로토타입을 기반으로 상속을 흉내내도롤 구현한다,

‘’’javascript
function Person () {}

Person.prototype.eyes = 2;
Person.prototype.nose = 1;

var kim = new Person();
var park = new Person();

console.log(kim.eyes); // 2
...
‘’’

Person.prototype이라는 빈 Object가 어딘가 존재하고, Person함수로부토 생성된 객체(kim, park)들은 어딘가에 존재하는 Object에 들어있는 값을 모두 갖다쓸 수 있습니다.
즉, kim과 park이 prototype에 선언된 값들을 가져다 사용할 수 있다.

자바스크립트에는 Prototype Link와 Prototype Object가 존재합니다. 이 둘을 통틀어 Prototype이라고 합니다.

Prototype Object

객체는 언제나 함수로 생성됩니다.

‘’’javascript
function Person() {};

var personObject = new Person; // 함수로 객체를 생성
‘’’

‘’’javascript
var obj = {};
var obj = new Object; // 위 코드는 이 코드와 같다.
obj // function Object() // Object도 함수다.
‘’’

Object와 마찬가지로 Function, Array도 모두 함수로 정의되어 있습니다.

함수가 정의될 때는 2가지 일이 동시에 이루어집니다.

1. 해당 함수에 constructor 자격 부여
    1. constructor 자격이 부여되면 new를 통해 객체를 만들어 낼 수 있습니다. 이것이 함수만 new 키워드를 사용할 수 있는 이유입니다. (constructor가 아니면 new 사용 불가)
2. 해당 함수의 Prototype Object 생성 및 연결



그리고 생성된 함수는 prototype이라는 속성을 통해 Prototype Object에 접근할 수 있습니다. Prototype Object는 일반적인 객체와 같으며 기본적인 속성으로는 constructor와 __proto__를 가지고 있습니다.

constructor는 prototype Object와 같이 생성되었던 한 함수를 가리키고 있다.
__proto__는 prototype Link이다.

해당 상황에서 kim과 park이 나왔던 예제를 다시보면 해보고 Person.prototype을 보면
constructor에 eyes, nose가 추가되어있다!

prototype link는 __proto__인데, 모든 객체가 빠짐없이 가지고 있습니다.

__proto__는 객체가 생성될 때 조상이였던 함수 prototype Object를 가리킵니다. kim의 객체는 person함수로부터 생성되었으니 person함수의 prototype object를 가리키고 있습니다.

kim.eyes를 요청하였을때, 먼저 kim의 속성을 확인하고, 없으면 상위 프로토타입을 탐색합니다. 최상위인 object의 prototype object까지 도달했는데도 못찾았을경우 undefined를 리턴합니다.

이렇게 __proto__ 속성을 통해 상위 프로토타입과 연결되어있는 형태를 프로토타입 체인이라고 합니다.
