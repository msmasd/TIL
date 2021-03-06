# javascript 기본

javascript기본에 대해서 정리하는 문서입니다.

## 1. 기본 문법

### 1.1 이벤트 위임(event delegation)

이벤트 위임은 이벤트 리스너를 하위 요소에 추가하는 대신 상위 요소에 추가하는 기법입니다. 리스너는 DOM의 event bubbling으로 인해 하위 요소에서 이벤트가 발생할 때마다 실행됩니다.

```javascript
// Get the parent DIV, add click listener...
document.getElementById("myDiv").addEventListener("click",function(e) {
	// e.target was the clicked element
  if (e.target && e.target.matches("a.classA")) {
    console.log("Anchor element clicked!");
	} else if (e.target && e.target.nodeName == "LI") {
    console.log("this is LI element clicked!");
  }
});
```

#### 1.1.1 이벤트 위임의 장점

* 각 하위 항목에 이벤트 핸들러를 연결하지 않고, 상위 요소에 하나의 단일 핸들러만 필요하기 때문에 메모리 사용 공간이 줄어듭니다.
* 제거된 요소에서 이벤트 핸들러를 해제하고 새 요소에 대해 이벤트를 바인딩할 필요가 없습니다.

### 1.2 event bubbling

DOM 요소에서 이벤트가 트리거되면 리스너가 연결되어 있는 경우 이벤트 처리를 시도한 다음, 해당 이벤트가 부모에게 bubbling되고 부모에서 같은 이벤트가 발생합니다. 이 bubbling은 요소의 최상단 부모요소인 document까지 계속적으로 발생시킴니다. 이벤트 bubbling은 이벤트 위임의 작동 메커니즘입니다.

### 1.3 [이벤트 전파를 막는 방법](https://programmingsummaries.tistory.com/313)

1. event.preventDefault(): 현재 이벤트의 기본 동작을 중단한다.(이후에 걸리는 모든 이벤트들은 동작하지않는다 ex: a태그에서 onclick이벤트를 넣었을때 href관련 코드가 동작하지않는다.)
2. event.stopPropagation(): 현재 이벤트가 상위로 전파되지 않도록 중단한다.
3. event.stopImmediatePropagation(): 현재 이벤트가 상위뿐 아니라 현재 레벨에 걸린 다른 이벤트도 동작하지 않도록 중단한다.(이벤트가 한개 이상인 경우에는 이것을 걸어줬을때 뒤에 있는 이벤트들은 동작하지않게된다.)
4. return false: jQuery를 사용할 떄는 위의 두개를 모두 수행한 것과 같고, 일반 javascript에서는 event.preventDefault()와 같다.

preventDefault를 한다고 해서 bubbling이 발생하지 않는것이 아니기때문에, 의도하지 않는 이벤트를 발생시키지 않을려면 event.preventDefault() 와 event.stopPropagation()을 동시에 사용하는 것이 좋다.

### 1.4 event object

click 이벤트 발생시 event object에서는 여러가지 props가 있는데 그중 주로 봐야하는 것들

1. target(object): 이벤트 타겟()
2. type(string): 이벤트의 타입(ex: "click")
3. currentTaget: 현재 이벤트가 실행되고 있는 타켓(target은 실제 이벤트가 일어난 DOM객체이고 currentTarget은 현재 이벤트가 발생되고 있는 DOM객체)
4. preventDefault(), stopPropagation(), stopImmediatePropagation(): event bubbling을 막는 함수들

### 1.5 console

console은 log메서드 말고도 다양한 메서드들이 있는데, log, dir, count, time, timeEnd 정도만 알면 좋다.

#### 1.5.1 log에서 값이 달라지는 경우

객체(object)를 로깅할 떄는 객체의 내용 변경사항이 실시간으로 업데이트 된다.(객체는 주소값을 가지고 있기 때문에 해당 주소가 가리키는 값이 계속 바뀌면 맨 마지막에 바뀐 값으로 보인다.)

해당 문제를 피할려면 객체를 **깊은복사**해서 로깅하거나, **객체가 아닌 값**을 로깅하면 됩니다.

#### 1.5.2 dir

객체는 dir, 나머지는 log로 로깅하면 된다. DOM 객체를 로깅하는데 사용
DOM 객체의 메서드가 뭐가 있는지 보고싶을때 사용하면 좋다.

```javascript
console.log(document.body); // <body>...</body>
console.dir(document.body); // aLink: "", accessKey: "", children: "", ...
```

#### 1.5.3 count

몇 번 호출되었나를 로깅하고 싶을때 사용합니다. 인자는 카운터의 이름입니다.

```javascript
console.count('카운터1'); // 카운터1: 1
console.count('카운터1'); // 카운터1: 2
console.count('카운터2'); // 카운터2: 1
console.count('카운터2'); // 카운터2: 2
console.count('카운터1'); // 카운터1: 3
```

#### 1.5.4 time, timeEnd

코드 수행 시간을 확인할때 유용합니다. 인자는 타이머의 이름입니다.

```javascript
console.time('타이머');
for (var i = 0; i < 1000000; i++) z = 5;
console.timeEnd('타이머') // 타이머: 6.76611325223ms
```

### 1.6. javascript의 `this`

`this`의 값은 함수가 호출되는 방식에 따라 달라집니다. `this`에 대한 규칙들입니다.

1. 함수를 호출할 때 `new`키워드를 사용하는 경우, 함수 내부에 있는 `this`는 완전히 새로운 객체입니다.
2. `apply`, `call`, `bind`가 함수의 호출/생성에 사용되는 경우, 함수 내의 `this`는 인수로 전달된 객체입니다.
3. `obj.method()`와 같이 함수를 메서드로 호출하는 경우, `this`는 함수가 프로퍼티인 객체입니다.
4. 함수가 자유함수로 호출되는 경우, 즉 위의 조건 없이 호출되는 경우 `this`는 전역객체입니다. 브라우저에서는 `window`객체입니다. 엄격모드(`use strict`)일 경우는 `this`는 `undefined`가 됩니다.
5. 위의 규칙 중 다수가 적용되면 더 상위 규칙이 적용되고 `this`값을 설정합니다.
6. 함수가 ES2015 화살표 함수인 경우 위의 모든 규칙을 무시하고 생성된 시점에서 주변 스코프의 `this`값을 받습니다.

[참조내용](https://codeburst.io/the-simple-rules-to-this-in-javascript-35d97f31bde3)

### 1.7 실행 컨텍스트

컨텍스트는 한국말로 번역하면 문맥입니다. 쉽게 코드의 실행 환경이라고 이해하면 됩니다.

### 1.8 scope

#### 1.8.1 전역 변수와 지역 변수

전역변수는 자바스크립트에서 제일 바깥 범위(**함수 안에 포함되지 않은**)에 변수는 만드는 것이다. 즉 **window**객체에 변수를 만드는 것이다.

#### 1.8.2 스코프(Scope)

지역변수는 아무리 해도 전역변수에 영향을 끼칠 수 없습니다. 함수 스코프는 함수 안에서 선언된 변수는 해당 함수 안에서만 사용할 수 있습니다. `function {}`안에서 선언한 것들은 함수 스코프에 속해진다. 만약 함수 스코프에 없는 변수값을 사용한다면 지역 스코프부터 전역 변수들이 있는 전역 스코프까지 점차 넓혀가며 찾기때문에 전역 변수에 접근 할 수 있다.

#### 1.8.3 스코프 체인

내부 함수에서는 외부 함수의 변수에 접근이 가능하지만 외부 함수에서는 내부 함수의 변수에 접근할 수 없습니다.
자기 자신의 스코프에서 찾고 한단계 위의 스코프에서 찾고 계속 찾다가 전역 스코프까지 찾다가 만약 변수를 못찾는다면 에러가 발생합니다. 이렇게 꼬리를 물고 계속 범위를 넓히면서 찾는 관계를 **스코프 체인**이라고 부릅니다.

#### 1.8.4 lexical scoping

**스코프**는 함수를 호출할 때가 아니라 **선언**할 때 생깁니다.


## 2. ES6 문법

## 3. 내가 찾아서 알게된 내용

### 3.1 get value from object by key string

자바스크립트의 오브젝트의 key를 string형으로 접근할 수 있다.
```javascript
const obj = {
  test: 1,
};
console.log('obj.test :', obj.test); // obj.test :1
console.log('obj[\'test\'] :', obj['test']); // obj['test'] : 1
```

### 3.2 object안의 값들을 불변하도록 만드는 방법(재할당은 되지만 값이 변경되지 않게하는것)

Object.freeze(obj)를 사용하면 된다.

```javascript
const obj = { a: 1 };
console.log('obj.a', obj.a); // 1
obj.a = 2;
console.log('obj.a', obj.a); // 2
Object.freeze(obj);
obj.a = 3; // freeze된 후에는 값이 변하지않는다.
console.log('obj.a', obj.a); // 2
```

### 3.3 함수의 call, apply, bind 메소드 [refer](https://www.zerocho.com/category/JavaScript/post/57433645a48729787807c3fd)

함수에도 기본 메소드가 있습니다.
apply, call, bind입니다.

#### 1. apply

원래 함수는 선언한 후 호출해야 실행됩니다. 호출되는 방법으로는 함수 뒤에 ()를 붙이는 것과, call 그리고 apply하는 방법입니다.

```javascript
const func1 = function(a, b, c) {
  return a + b + c;
};
func1(1, 2, 3);
func1.call(null, 1, 2, 3);
func1.apply(null, [1, 2, 3]);
```
call은 보통 함수와 똑같이 인자를 넣고, apply는 인자를 하나로 묶어 배열로 만들어 넣습니다. 여기서 공통적으로 있는 null은 this를 대체하는 용도입니다.

```javascript
var obj = {
  string: ‘zero’,
  yell: function() {
    alert(this.string);
  },
};
var obj2 = {
  string: ‘what?’,
};
obj.yell(); // ‘zero’
obj.yell.call(obj2); // ‘what?’
```

마지막줄에서 `obj.yell.call(obj2)`로 this가 가리키는 것을 obj에서 obj2로 바꾸었습니다. yell은 obj의 메소드인데도 ‘zero’대신에 ‘what?’이 alert되었습니다. 즉 다른 객체의 함수를 자기것마냥 사용할 수 있습니다.

기본적으로 `this`는 `window`로 동작합니다. 몇 가지 방법으로 window를 다른 것으로 바꿀 수 있습니다. call, apply, bind에서 첫 번째 인자로 다른 것을 넣어주는게 this를 바꾸는 방법 중 하나입니다.

위 메소드를 쓰는 예로, 함수의 **arguments**를 조작할 때 사용합니다. **arguments**는 함수에 들어온 인자를 배열 형식으로(배열은 아닙니다. **유사배열**이라고 부릅니다) 반환합니다.

```javascript
function example() {
  console.log(arguments);
};
example(1, ‘string’, true); // [1, ‘string’, true]
// 생긴 건 배열이지만, 배열이 아니라 유사 배열이기 때문에, 배열의 메소드는 쓸 수 없습니다.
```

```javascript
function example2() {
  console.log(arguments.join());
};
example2(1, ‘string’, true); // Uncaught TypeError: arguments.join is not a function
```
에러가 발생합니다. arguments는 모양만 배열이지 실제 배열이 아니라서 배열의 메소드를 사용하면 에러가 발생합니다. 이때 call, apply가 사용되어집니다.

```javascript
function example3() {
  console.log(Array.prototype.join.call(arguments));
};
example3(1, ‘string’, true); // ‘1,sring,true’
```
배열의 prototype에 있는 join 함수를 빌려 씁니다. this는 arguments를 가리키게 하고, join 외에도 slice, concat 등등 모든 메소드를 이 방식으로 사용할 수 있습니다.

bind 함수는 함수가 가리키는 this만 바꾸고 호출하지 않는 겁니다.
```javascript
var obj = {
  string: ‘zero’,
  yell: function() {
    alert(this.string);
  },
};
var obj2 = {
  string: ‘what?’,
};
var yell2 = obj.yell.bind(obj2);
yell2(); // ‘what?’
```
bind를 통해 this를 obj2로 만들어 놓은 함수를 yell2로 만들고 나중에 호출시킨다.

### 3.4 Math 객체 관련 내용

```javascript
var a = 0.1;
var b = 0.2;
a + b; // 0.30000000000004
```

a + b의 값이 0.30000000004인 이유는 컴퓨터는 소수를 2진법으로 바꿔서 계산하는데 2진법으로 바꾸면 몇몇 소수는 무한소수가 됩니다. 저장공간이 정해져있는 컴퓨터는 무한소수의 값을 다 저장하지 못하고 끝부분을 버텨 유한소수로 만듭니다. 그 버린 부분때문에 오차가 발생합니다.

해결하는 방법은 여러가지 있다.
``(10 * a + 10 * b) / 10 // 0.3``

혹은 toFixed, toPrecision을 통해서 자릿수를 정한다.

```javascript
(a + b).toFixed(2); // '0.30'
0.00125.toPrecision(2); // '0.0013'
1.2356.toFixed(3); // '1.236'
1.2346.toPrecision(2); // '1.2'
```

1. toFixed: 지정된 소수자릿수까지 반올림해서 나타냅니다. 리턴은 문자열이기 때문에 숫자로 바꿔줘야 합니다.
2. toPrecision: 지정된 자릿수만큼만 표현해 문자열로 반환합니다. 만약 소수인 경우 앞의 0들은 무시됩니다.

isNaN(숫자)는 안에 넣은 숫자가 진짜 숫자인지 테스트합니다.
NaN은 Not a Number로 `'3 / "가"`처럼 숫자가 아닌 계산을 하면 NaN값이 나옵니다.

parseInt(숫자, 진법), parseFloat(숫자)

```javascript
parseInt("1등", 10); // 1
parseFloat("0.5달러"); // 0.5
parseFloat(0.3.toFixed(2)); // 0.3
```

문자열이더라도 숫자로 시작하면 뒤에 글자들은 없애고 숫자로 바꿔줍니다. 숫자로 시작하지않으면 NaN이 됩니다.

Number(아무거나)

```javascript
Number("1"); // 1
Number("0.5달러"); // NaN
```

Number객체 자체가 함수로 쓰일 수 있습니다.

배열.reduce(function(prev, cur) {
  조건
})

배열을 왼쪽부터 조건을 적용해 하나의 값으로 만드는 함수

### 3.4 Window 객체와 BOM

Windows객체는 브라우저의 요소들과 자바스크립트 엔진, 그리고 모든 변수를 담고 있는 객체입니다.

브라우저 **전체**를 담당하는 게 **Window** 객체이고, **웹사이트만** 담당하는게 **Document** 객체입니다.

Document도 Window 객체 안에 들어있습니다.

주요 Window객체의 속성과 메소드는 screen, location, history, documnet 같은 객체들이 있습니다.
메소드는 parseInt, isNaN이 있습니다.

Window는 모든 객체의 조상입니다. *전역객체*(글로벌객체)라고 하는데, 모든 객체를 다 포함하고 있기 때문에 window는 그냥 **생략가능**합니다.

1. window.close(): 현재 창을 닫습니다.
2. window.open(주소, 탭설정, 새 창에 대한 각종 설정 전달): 새창, 팝업 창의 형태로 열거나 새탭으로도 열 수 있습니다. 탭설정을 통해 새탭, 현재탭에서 열지 정할 수 있습니다. 세번째 탭을 통해 가로 세로 크기도 지정가능

```javascript
open('https://zerocho.herokuapp.com'); // 새탭
open('https://zerocho.herokuapp.com', '_self'); // 현재 탭
open('', '' , 'width=200, height=200'); // 가로, 세로 200px의 팝업창

var popup = window.open('', '', 'width=200, height=200');
popup.document.write('안녕하세요'); // 새창의 내용 변경
popup.close();
```

3. window.encodeURI(), window.decodeURI() : 주소에 한글이 들어가면 URI인코딩 및 디코딩 처리를 할 수 있는데, window객체로 바꿀 수 있다.

4. setTimeout(함수, 밀리초), window.setInterval(함수, 밀리초)

```javascript
setTimeout(function() {
  alert('1초 뒤');
}, 1000);

setInterval(function() {
  console.log('1초마다');
}, 1000);
```

5. window.getComputedStyle(태그)

``console.log(getComputedStyled(document.getElementById('app-root')));``
를 통해 태그의 스타일을 찾는 메소드입니다.

document는 DOM이라고 불리고 나머지는 브라우저에 대한 정보를 가지고 있어서 BOM(Brower object model)이라고 불립니다.

#### BOM

1. navigator: 브라우저, 운영체제(OS) 대한 정보가 있습니다.(`navigator.userAgent`)
userAgent 정보를 바탕으로 분석 사이트에서는 고객에 대한 정보를 분석합니다. 여기서 사용자의 브라우저를 체크할 수 있습니다.

``navigator.userAgent; // "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36"``

그외에는

```javascript
navigator.language; // "ko"
navigator.cookieEnabled; // true
navigator.vendor; // "Google Inc"
```

2. screen: 화면에 대한 정보를 알려줍니다. 너비(width), 높이(height), 픽셀(pixelDepth), 컬러(colorDepth), 화면 방향(orientation), 작업표시줄을 제외한 너비와 높이(availWidth, availHeight)등등 있습니다.

3. location: 주소에 대한 정보를 알려주고(protocol, host, hostname, pathname, href, port, search, hash 속성을 이용). `location.reload()`로 새로고침도 가능합니다. `location.replace()`는 현재 주소를 다른 주소로 교체합니다.(다른페이지로 가지만 이전 페이지 기록이 남지 않습니다.)

```javascript
location.host; // "www.zerocho.com"
location.hostname; // "www.zerocho.com"
location.protocol; // "https:"
location.href; // "https://www.zerocho.com/category/Javascript/post/..."
location.pathname; // "/category/Javascript/post/..."
```

5. history: 앞으로가기(`history.forward()` or `history.go(1)`), 뒤로가기(`history.back()` or `history.go(-1)`) 같은 것을 할수있습니다.

### Hoisting

### javascript Event Loop

