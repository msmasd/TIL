# component

## 1.1 정의

개념적으로는 컴포넌트는 Javascript 함수와 유사하다. "props"라고 하는 임의의 입력을 받은 후, 화면에 어떻게 표시되는지를 기술하는 React 엘리먼트를 반환한다.

React.Component의 subclass로 정의할떄 ``render()``함수를 반드시 호출해야한다.(다른 함수들은 option이다.)
**React에서 상속보다 합성(composition)을 통하여 코드 재사용성을 잘 사용해라

## 1.2 컴포넌트의 라이프 사이클

![simple_react_life_cycle](https://github.com/msmasd/TIL/blob/master/document/React/image/simple_react_life_cycle.PNG)
간단한 라이프 사이클

![react_life_cycle](https://github.com/msmasd/TIL/blob/master/document/React/image/react_life_cycle.PNG)
디테일한 라이프 사이클

## 1.2 함수 컴포넌트와 클래스 컴포넌트

```javascript
// 함수형 컴포넌트
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
// es6 함수형
const Welcome = (props) => (
  <h1>Hello, {props.name}</h1>
);
// class사용
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {props.name}</h1>
  }
}
```

## 1.3 컴포넌트 렌더링

```javascript
const element = <Welcome name="Sara" />;
```

React 엘리먼트는 사용자 정의 컴포넌트로 나타낼 수 있다. React가 사용자 정의 컴포넌트로 작성한 엘리먼트를 발견하면 JSX attribute를 해당 컴포넌트에 단일 객체로 전달한다.(이 객체를 props라고 한다.)


```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}

const element = <Welcome name="SangMin" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

이 예시에서는 다음과 같은 일들이 일어난다.
1. `<Welcome name="SangMin" />` 엘리먼트로 `ReactDOM.render()`를 호출한다.
2. React는 `{name: 'SangMin'}`를 props로 하여 `Welcome` 컴포넌트를 호출한다.
3. `Welcome` 컴포넌트는 결과적으로 `<h1>Hello, SangMin</h1>`엘리먼트를 반환한다.
4. ReactDOM은 `<h1>Hello, SangMin</h1>` 엘리먼트와 일치하도록 DOM을 효율적으로 업데이트한다.

## 1.4 props는 read-only

함수 컴포넌트나 클래스 컴포넌트 모두 컴포넌트의 자체 props를 수정하면 안된다. -> **모든 React 컴포넌트는 자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 한다**

그러므로 props의 값을 수정하면 안된다.


## 종류

1. 일반 component
2. pure component: React.component와 거의 비슷하다. 단지 `shouldComponentUpdate()`가 미리 구현되어있는것이다. *구현은 props와 state를 이용한 얕은(shallow) 비교를 통한 구현*
3. functional component: stateless한 component이고 단지 props를 인자로 받아 react element를 리턴해주는 자바스크립트 함수이다.

```javascript
return (
  !shallowEqual(oldProps, newProps) ||
  !shallowEqual(oldState, newState)
);
```
**참고 얕은 복사 구현부**


stateless인 이유는 안에서 `this.state`에 접근할 수 없기 때문이다. 그리고 lifecycle또한 사용하거나 other hooks를 사용할 수 없다.

react element는 2 properties를 가진다.(실제로는 더 가지고있지만 여기서 이해를 위해서는 2개만 있다고 가정) *type(string)*, *props(object)* 이 두개다.
```javascript
{
  type: 'div',
  props: {
    children: props.name,
  }
}
```
react가 우리의 stateless 컴포넌트를 렌더할때 필요한 모든것은 해당 component function을 호출하고 props를 pass해주는것이다.(번역이 매끄럽지않아 원문 첨부 *When react renders our stateless component all what it needs to do is just call MyStatelessComponent function and pass props there. That’s it.*)


Component class는 state, lifecycle hooks 등등 더 복잡하게 되어있다. 그래서 React는 Compoentn class를 렌더할때 위에 말한 복잡한 것들을 다 가져오고 초기화 해줘야한다.
이것은 ES5로 변환하였을때 더 차이가 나는데,
functional Component
```javascript
var MyStatelessComponent = function MyStatelessComponent(props) {
  return React.createElement(
    "div",
    null,
    props.name
  );
}
```

component class

```javascript
var _createClass = function () { function defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } } return function (Constructor, protoProps, staticProps) { if (protoProps) defineProperties(Constructor.prototype, protoProps); if (staticProps) defineProperties(Constructor, staticProps); return Constructor; }; }();
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }
function _possibleConstructorReturn(self, call) { if (!self) { throw new ReferenceError("this hasn't been initialised - super() hasn't been called"); } return call && (typeof call === "object" || typeof call === "function") ? call : self; }
function _inherits(subClass, superClass) { if (typeof superClass !== "function" && superClass !== null) { throw new TypeError("Super expression must either be null or a function, not " + typeof superClass); } subClass.prototype = Object.create(superClass && superClass.prototype, { constructor: { value: subClass, enumerable: false, writable: true, configurable: true } }); if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass; }
var MyComponentClass = function (_React$Component) {
  _inherits(MyComponentClass, _React$Component);
function MyComponentClass() {
    _classCallCheck(this, MyComponentClass);
return _possibleConstructorReturn(this, (MyComponentClass.__proto__ || Object.getPrototypeOf(MyComponentClass)).apply(this, arguments));
  }
_createClass(MyComponentClass, [{
    key: "render",
    value: function render() {
      return React.createElement(
        "div",
        null,
        this.props.name
      );
    }
  }]);
return MyComponentClass;
}(React.Component);
```

이렇게 차이가 난다. 실제로 builder를 통해 만들어지는 파일 크기는 1.2kb vs 97 byte이다.
하지만 실제 성능에서는 stateless컴포넌트가 마냥 좋은것은 아니다. 복잡한 component에서는 shouldComponentUpdate를 잘 처리한다면 stateless컴포넌트보다 더 좋은 성능을 보여줄 수 있다.
결론적으로 stateless component vs state component를 선택하는것은 state, lifecycle, hooks등을 사용하지않고 간단히 component를 만든다면 stateless component, 혹시 위에 기능들을 사용해야 한다면 state component이다.

[Class component vs functional component 참고 사이트](https://itnext.io/react-component-class-vs-stateless-component-e3797c7d23ab)

### HOC

## 추천 패턴

### smart and dumy component

사용이유
랜더 관련하여 처리부분
ShouldComponentUpdate 부분에서 이 패턴을 사용하지않으면 이슈 생기는 부분 언급
