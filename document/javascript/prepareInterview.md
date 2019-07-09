# 인터뷰 준비

## 자바스크립트 코딩 면접에서 알고있어야 할 3가지 질문 [링크](https://joshua1988.github.io/web-development/javascript/javascript-interview-3questions/)

### 1. 이벤트 위임하기

어플리케이션을 제작할 때 사용자가 페이지 요소를 조작할 수 있도록 페이지의 버튼, 텍스트, 이미지 등에 이벤트를 붙여야 할 때가 있습니다. 예를 들어, 면접에서 면접관이 todo 리스트 어플리케이션 제작에 대해 질문하는 경우 "해당 리스트의 아이템에 대해서 사용자가 클릭할 때 이벤트가 일어나도록 구현하라"라고 하면 아래와 같은 샘플을 준다
```html
<ul>
  <li class="item">Walk the dog</li>
  <li class="item">Pay bills</li>
  <li class="item">Make dinner</li>
</ul>
```

```javascript
document.addEventListener('DOMContentLoaded', function() {
  let app = document.getElementById('todo-app');
  let items = app.getElementsByClassName('item');

  // 각 아이템에 이벤트 리스터를 등록합니다.
  for (let item of items) {
    item.addEventListener('click', function() {
      alert('you clicked on item: ', item.innerHTML);
    });
  }

});
```

위 코드는 제대로 동작하지만 **문제점**은 리스트의 아이템 각각에 이벤트를 붙이고 있는 것입니다. 아이템 요소가 10000개 라면 위 함수는 10000개의 분리된 이벤트 리스너를 생성하고 그걸 각각 DOM에 등록할 것 입니다. 이것은 매우 비효율 적입니다.


이런 면접에서는 먼저 면접관에게 사용자가 최대로 입력할 수 있는 요소의 개수를 물어보는것이 좋습니다. 최대 갯수가 10개가 넘지 않는다면, 위 코드는 문제없습니다. 하지만 사용자가 입력할 수 있는 아이템의 수가 무한개라면 더 효율적인 해결책을 찾아야합니다.


아이템 갯수마다 이벤트 리스너를 생성, 등록 하는 것보다는 모든 아이템 리스트에 대해서 한 개의 이벤트 리스너를 생성하여 전체 영역에 등록하는 것이 훨씬 효율적입니다. 그렇게 하면 사용자가 해당 아이템을 선택했을 때 이벤트 리스너가 해당 아이템에 대해서 이벤트를 발생시킵니다. 이 것을 우리는 ***이벤트 위임*** 이라고 합니다.

```javascript
document.addEventListener('DOMContentLoaded', function() {

  let app = document.getElementById('todo-app');

  app.addEventListener('click', function(e) {
    if (e.target && e.target.nodeName === 'LI') {
      let item = e.target;
      alert('you click on item :' + item.innerHTML);
    }
  });
});
```

### 2. 루프에서 클로져 이용하기

클로져는 이너함수가 스코프 밖에 있는 변수에 접근하는 것입니다. 클로져는 보통 정보은닉을 구현하거나 함수 팩토리를 생성할 때 사용됩니다.

*정수 값을 갖는 리스트를 반복문으로 접근하여 해당 요소를 3초를 지연시키고 값을 출력하라*

일반 개발자들은 위 질문을 아래와 같이 구현합니다.

```javascript
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  setTimeout(function() {
    console.log('The index of This number is: ' + i);
  }, 3000);
}
```

위 코드를 실행시키면 출력값은 모두 4로 찍힙니다. setTimeout함수가 인덱스 i를 반복하는 스코프 밖의 스코프를 갖는 클로져를 생성하기 때문입니다. 3초가 지난 후에 클로저가 실행되고 i 값을 출력할 때 반복문의 종료 값인 4를 출력합니다. 이는 setTimeout의 스코프와 for 반복문 안의 스코프가 다르기 떄문에 발생합니다. 위 문제를 해결하는 방법은 2가지가 있습니다.

```javascript
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  setTimeout(function(i_local) {
    return function() {
      console.log('The index of This number is: ' + i_local);
    }
  }(i), 3000);
}

// 혹은
for (let i = 0; i < arr.length; i++) {
  // let은 함수가 호출 될 때 마다 인덱스 i 값이 바인딩 되는 새로운 바인딩 기법을 사용합니다.
  // 참고 http://exploringjs.com/es6/ch_variables.html#sec_let-const-loop-heads
  setTimeout(function() {
    console.log('The index of This number is: ' + i);
  }, 3000);
}
```

### 3. 디바운싱(Debouncing)

브라우저 이벤트 중에는 윈도우 크기를 재조정 하거나 페이지 스크롤을 내리는 등의 매우 짧은 시간에 다수 발생되는 이벤트들이 있습니다.


면접에서 어플리케이션 제작에 대해 논할 때, 스크롤링이나 화면 재조정 그리고 키 눌림과 같은 이벤트에 대해서는 페이지 속도와 성능을 향상시키기 위한 디바운싱(Debouncing) 또는 쓰로틀링(Throttling)를 꼭 짚고 넘어가야 합니다.

디바운싱은 실제로 함수가 다시 호출되기 전까지 시간 간격을 두어 성능 이슈를 해결하는 한가지 방법입니다. 다비운싱의 올바른 구현 방법은 몇 가지 함수 호출을 한 개의 그룹으로 묶고 특정 시간이 지난 후에만 호출될 수 있도록 구조화하는 것입니다.

```javascript
function debounce(fn, delay) {
  // 타이머 선언
  let timer = null;
  // 타이머 변수에 접근 가능한 클로져 함수
  return function() {
    // 클로져 함수 안에서 this와 arguments 변수로 디바운싱 함수의 스코프와 변수를 접근한다
    let context = this;
    let args = arguments;
    // 만약 이벤트가 호출되면 타이머를 초기화 하고 다시 시작한다.
    clearTimeout(timer);
    timer = setTimeout(function() {
      fn.apply(context, args);
    }, delay);
  }
}

// 사용자가 스크롤 할 때 호출되는 이벤트 함수
function foo() {
  console.log('You are scrolling!');
}

// 이벤트 함수를 디바운싱 함수로 감싸서 2초마다 발생하도록 한다.
let elem = document.getElementById('container');
elem.addEventListener('scroll', debounce(foo, 2000));
```