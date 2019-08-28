# immer

immer는 javascript에서 immutable을 도와주는 라이브러리입니다.
immer는 immutable.js와 다르게 plain javascript object를 사용하여 immutable화된 데이터를 사용할 수 있습니다.

방법도 굉장히 간단합니다.

## 1. 사용방법

```javascript
produce(currentState, producer: (draftState) => void): nextState

const toBeImmutable = {
  test1: 1,
  test2: 2,
};
const toBeImmutable = produce(toBeImmutable, draft => {
    draft.test1 = 2
});
```

위와 같이 produce함수를 사용하여 그 안에서 draft값을 변경하면 변경된 값에 대해서 immutable한 값이 만들어집니다.

Immer로 값을 변경한 경우에는 그 해당 값을 가지고 있는 모든 부모의 주소값이 갱신된다.
Ex) const t1 = { t2: { t3: 1 } };
t1.t2.t3 = 2로 immer를 통해 바뀌면 오브젝트 객체인 t1, t2의 주소값이 변경되어진다!
