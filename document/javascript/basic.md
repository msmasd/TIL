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