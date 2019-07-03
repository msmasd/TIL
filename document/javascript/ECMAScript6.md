# ECMAScript6

ECMAScript2015 혹은 ES6로 알려진 ECMAScript6는 Ecma international의 ECMA-262 기술 규격에 정의된 표준화된 스크립트 프로그래밍 언이입니다. ES6는 요즘 많이 쓰이는 React, Vue에서 주로 사용되어지고 있고, 기존 자바스크립트를 사용하는것 보다 더 편리하게 사용할 수 있기 때문에 배우면 좋다고 생각합니다.

## 1. 주요 기능

### 1.1 arrows

Arrows 함수는 **=>** 문법을 사용하는 축약형 함수입니다.

#### 특징

1. Arrows는 표현식의 결과 값을 반환하는 표현식 본문(expression bodies)뿐만 아니라
2. 상태 블럭 본문(statement block bodies)도 지원합니다.
3. 하지만 일반 함수의 자신을 호출하는 객체를 가리키는 dynamic this와 달리 arrows 함수는 코드의 상위 스코프(lexical scope)를 가리키는 lexical this를 가집니다.

```javascript

var evens = [2, 4, 6, 8];
var result = [];

// 1번 특징(표현식의 결과가 return)
var odds = evens.map(v => v + 1);

// 2번 특징(블럭 내부를 실행만 함)
odds.forEach(v => {
  if ( v % 5 === 0)
    result.push(v);
})

// 3번 특징
var bob = {
  _name: "Bob",
  _friends: ["John", "Brian"],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

### 1.2 classes

ES6 클래스는 프로토타입 기반 객체지향 패턴을 더 쉽게 사용할 수 있는 대체제입니다. 클래스 패턴 생성을 더 쉽고 단순하게 생성할 수 있어서 사용하기도 편하고 상호운용성도 증가됩니다.

```javascript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(generators, materials);

    this.bones = [];
    // ...
  }
  update(camera) {
    // ...
    super.update();
  }
  get boneCount() {
    return this.bones.length;
  }
  set bones(bones) {
    this.bones = bones;
  }
}
```

### 1.3 enhanced object literals

ES6에서 객체 리터럴은 선언문에서 프로토타입 설정, ``foo: foo`` 선언을 위한 단축 표기법, 메서드 정의, super 클래스 호출 및 동적 속성명을 지원하도록 향상 되었습니다.

```javascript
var obj = {
  __proto__: theProtoObj,

  handler,

  toString() {
    return "d" + super.toString();
  },
  ['prop_' + (() => 42)() ] : 42 // Computed (dynamic) property names
};
```

### 1.4 template strings

Templates String(ES6부터는 Template literals라 부름)는 문법적으로 더 편하게 string을 생성할 수 있게합니다.

```javascript
// 기본 문자 스트링 생성
`In Javascript '\n' is a line-feed.`

// 멀티 라인 string
`In Javascript this is
 not legal.`

var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

### 1.5 destructuring

배열과 객체에 패턴 매칭을 통한 데이터 바인딩을 제공합니다. Destructuring는 할당 실패에 유연하며, 실패 시 undefined값이 자동 할당됩니다.

```javascript
// list matching
var [a, , b] = [1, 2, 3];

// object matching
var { op: a, lhs: { op: b }, rhs: c } = getASTNode();

var {op, lhs, rhs } = getASTNode();

function g({name: x}) {
  console.log(x);
}

var [a] = [];
a === undefined;

var [a = 1] = [];
a === 1;
```


### 1.6 default + rest + spread

파라미터에 기본 값을 설정할 수 있습니다.

```javascript
function f(x, y=12) {
  return x + y;
}
f(3) // 15

function f1(x, ...y) {
  // y is an Array ["hello", true];
  return x * y.length;
}
f1(3, "hello", true); // 6

function f2(x, y, z) {
  return x + y + z;
}
f(...[1, 2, 3]);
```

### 1.7 let + const

let은 var와 유사하게 재할당이 가능하고 재선언은 안된다., const는 상수처럼 재할당, 재선언이 안됩니다.
var의 유효범위는 전체 외부함수지만, let, const는 변수를 선언한 블록과 그 내부 블록에서만 유효합니다.

```javascript
function f() {
  {
    let x;
    {
      const x = "sneaky"
      x = "foo" // error
    }
    let x = "inner" // error, 재선언 불가
  }
}
```

### 1.8 iterator + for ... of

Iterator 객체는 CLR의 IEnumerable 혹은 Java의 Iterable처럼 사용자 정의의 반복을 가능하게 해줍니다. `for..of` 반복문이 ES6에서 추가되었으며, `for..in` 반복문과 달리 iterator 기반의 컬렉션 전용 반복문입니다.

```javascript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;

    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur };
      }
    }
  }
}

for (var n of fibonacci) {
  if (n > 1000)
    break;
  console.log(n) /// 1, 2, 3, 8, ...987
}
```

### 1.9 generators

`function*`와 `yield`키워드를 이용하여 iterator 선언을 단순하게 작성할 수 있게 도와줍니다.
`function*`로 선언한 함수는 `Generator`객체를 반환합니다. `Generator`는 iterator의 하위 타입이며 next와 throw 메서드를 가지고 있습니다. 이 메서드들로 인해 `yield`키워드로 반환된 값은 다시 generator에 주입하거나 예외처리를 할 수 있게 되었습니다.

```javascript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      [pre, cur] = [cur, pre + cur];
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  if (n > 20)
    break;
  console.log(n); // 1, 2, 3, 5, 8, 13
}

function* gen() {
  yield* ["a", "b", "c"];
}

var a = gen();

a.next(); // { value: "a", done: false }
a.next(); // { value: "b", done: false }
a.next(); // { value: "c", done: false }
a.next(); // { value: undefined, done: true }
```

### 1.10 unicode

### 1.11 modules

### 1.12 map + set + weakmap + weakset

### 1.13 proxies

### 1.14 symbols

### 1.15 subclassable built-ins

### 1.16 promises

### 1.17 math + number + string + array + object APIs

### 1.18 binary and octal literals

### 1.19 reflect api

### 1.20 tail calls

