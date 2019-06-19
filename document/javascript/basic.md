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