# Redux Saga

리액트/리덕스 애플리케이션의 사이드 이펙트, 예를 들면 데이터 fetching이나 브라우저 캐시에 접근하는 순수하지 않는 **비동기** 동작들을, 더 쉽고 좋게 만드는 것을 목적으로하는 라이브러리입니다.

saga는 애플리케이션에서 사이드 이펙트만을 담당하는 별도의 쓰레드와 같은 것으로 보면 됩니다. `redux-saga`는 리덕스 미들웨어입니다. 따라서 앞서 말한 쓰레드가 메인 애플리케이션에서 일반적인 리덕스 엑션을 통해 실행되고, 멈추고, 취소될 수 있게 합니다. 또한 모든 리덕스 애플리케이션의 상태에 접근할 수 있고 리덕스 액션 또한 dispatch 할 수 있습니다.

## 튜토리얼

```javascript
// ...
import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'

// ...
import { helloSaga } from './sagas'

const sagaMiddleware = createSagaMiddleware()
const store = createStore(
  reducer,
  applyMiddleware(sagaMiddleware)
)
sagaMiddleware.run(helloSaga)

const action = type => store.dispatch({type})
```
위와 같이 store에 사가를 연결하여 saga 엑션을 실행할 수 있는 환경 세팅

```javascript
import { delay } from 'redux-saga'
import { put, takeEvery } from 'redux-saga/effects'

// worker Saga: 비동기 증가 태스크를 수행할겁니다.
export function* incrementAsync() {
  yield delay(1000)
  yield put({ type: 'INCREMENT' })
}

// watcher Saga: 각각의 INCREMENT_ASYNC 에 incrementAsync 태스크를 생성할겁니다.
export function* watchIncrementAsync() {
  yield takeEvery('INCREMENT_ASYNC', incrementAsync)
}
```
Sagas는 오브젝트들을 redux-saga 미들웨어에 yield하는 **제네레이터 함수**로 구현되었습니다. yield된 오브젝트들은 미들웨어에 의해 해석되는 명령의 한 종류입니다. Promise 가 미들웨어에 yield 될 때, 미들웨어는 Promise가 끝날때까지 Saga를 **일시정지** 시킬것 입니다. 위의 예시에서 `incrementAsync` Saga는 1초 후에 일어날 delay의 resolve에 위해 Promise가 리턴될때 까지 정지되어있을겁니다.

Promise가 한번 resolved되고 나면, 미들웨어는 Saga를 다시 작동시키면서, 다음 yield까지 코드를 실행합니다. 이 예제에서 다음 상태는 미들웨어에게 `INCREMENT` 액션을 dispatch 하게 알려주는 `put({type:'INCREMENT'})`의 결과 객체가 됩니다.

```javascript
const gen = incrementAsync(); // incrementAsync함수는 제네레이터 함수이기때문에 이것을 실행하면 이터레이터 오브젝트를 반환하고,이터레이터의 next메소드는 객체를 리턴한다. { done: boolean, value: any }
gen.next(); // { done: false, value: <result of calling delay(1000)> }
gen.next(); // { done: false, value: <result of calling put({type: 'INCREMENT' })> }
gen.next(); // { done: true, value: undefined }
```

## 헬퍼 함수
`redux-saga`는 스토어에 몇몇 지정된 액션들이 dispath 되었을때 태스크를 만들귀 위해 내부 함수들을 감싸는 몇몇 헬퍼 이펙트를 제공합니다.

### 1. `takeEvery`

여러개의 `incrementAsync` 인스턴스를 동시에 시작되게 합니다. 한개 혹은 여러개의 아직 종료되지 않은 `incrementAsync`태스크들이 있더라도 새로운 `incrementAsync` 태스크를 시작할 수 있습니다. -> 만약 마지막으로 발생된 리퀘스트의 응답만 얻고 싶다면 `takeLatest` 헬퍼를 사용하면 된다.

## 서술적 이펙트

Saga 로직을 표현하기 위해서 우리는 제너레이터로부터 온 순수 자바스크립트 객체를 yield 합니다. 이런 오브젝트를 **이펙트**라고 부릅니다. 이펙트는 미들웨어에 의해 해석되는 몇몇 정보들을 담고있는 간단한 객체입니다. 어떤 기능을 수행하기 위해 미들웨어에 전해지는 명령(store에 액션을 disptach(put)하는 행위나 비동기 함수를 호출하는 등)이 있다.

Saga는 여러 형식으로 이펙트들을 yield 할 수 있습니다.

1. call: 비동기 요청을 수행할 방법.(fetch 요청을 즉시하지 않고 비동기로 처리한다.) `CALL`은 이펙트에 대한 설명을 생성한다. `call`은 함수 호출을 설명하는 순수 객체를 리턴하는 함수기 때문에 제너레이터를 Redux 환경 바깥에서 쉽게 테스트 할수 있습니다.

2. put: 스토어에 액션 dispatch하기(`dispatch` 이펙트를 생성하는 함수는 `put`이다.)

try, catch를 사용하여 api가 error발생했을때를 대비한 코드를 작성 할 수 있다.(api호출시 error났을때 error관련 put을 처리함)

## 액션 풀링

```javascript
import { select, takeEvery } from 'redux-saga/effects'

function* watchAndLog() {
  yield takeEvery('*', function* logger(action) { // *(와일드카드)를 통해 모든 액션을 캐치할 수 있다
    const state = yield select()

    console.log('action', action)
    console.log('state after', state)
  })
}
```

```javascript
import { select, take } from 'redux-saga/effects'

function* watchAndLog() {
  while (true) {
    const action = yield take('*')
    const state = yield select()

    console.log('action', action)
    console.log('state after', state)
  }
}
```
두개의 구현내용은 동일하지만, `takeEvery`를 사용한것과 `take`와 `whiile`을 사용하여 똑같이 구현한 내용이다.

`take`는 `call`, `put`와 비슷하다. 이는 특정한 액션을 기다리기 위해서 미들웨어에 알려주는 명령 오브젝트를 생성합니다. `call`이펙트는 미들웨어가 프로미스의 resolve를 기다리게 하고, `take`의 경우에는 미들웨어가 매칭되는 액션이 dispatch될 때까지 기다린다.

## non-blocking 호출

`fork`는 `call`과 다르게 비동기호출이긴 하지만 resolve가 되기전까지 로직의 흐름이 아래로 내려가지않는다. 특정 상황에서 api호출후 아래 로직을 태우면서 비동기처리를 해야한다면 `fork`를 통해 해당 api처리하는 제너레이터함수를 돌리고 아래 로직을 태우는 방식으로 처리하면된다. `fork`된 태스크를 취소하는 이펙트는 `cancel`이다.

## 여러 이펙트의 경주

`race` 이펙트를 통해 경주에서 진 이펙트를 자동으로 취소해준다.

```javascript
import { race, take, put } from 'redux-saga/effects'
import { delay } from 'redux-saga'

function* fetchPostsWithTimeout() {
  const {posts, timeout} = yield race({
    posts: call(fetchApi, '/posts'),
    timeout: call(delay, 1000)
  })

  if (posts)
    put({type: 'POSTS_RECEIVED', posts})
  else
    put({type: 'TIMEOUT_ERROR'})
}
```
여기서 `posts`나 `timeout` 둘중에 어느 하나라도 먼저 resolve가 되는것만 처리되고 다른것은 취소된다.

> 참고자료
1. [redux-saga git book(한글번역)](https://mskims.github.io/redux-saga-in-korean/introduction/BeginnerTutorial.html)