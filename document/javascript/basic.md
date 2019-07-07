# javascript 기본

javascript기본에 대해서 정리하는 문서입니다.

## 1. 기본 문법

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
