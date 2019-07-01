# CSS

Cascading(연속적인) Style Sheet: 상위 요소의 스타일 속성을 자손 요소들에게 상속시켜주는 모습이 DOM 트리구조에서 마치 폭포수처럼 내려가는 모습을 닮았기 때문이다.

CSS는 HTML과 다른 언어이다. 그래서 브라우저 엔진은 HTML, CSS를 따로 parsing한 뒤, 내용을 합쳐서 브라우저에 보여준다.

1. Load HTML
2. Parse HTML
3. "콘텐츠 트리" 내부에서 태그를 DOM 노드로 변환
4. 외부 CSS파일과 함께 포함된 스타일 요소 파싱
5. 스타일 정보와 HTML 표시 규칙은 "렌더 트리"라고 부르는 또 다른 트리를 생성
6. 렌더 트리 생성이 끝나면 배치가 시작.(각 노드가 화면의 정확한 위치에 표시되는 것을 의미)

> [네이버D2 브라우저는 어떻게 동작하는가?](https://d2.naver.com/helloworld/59361)

## 사용법

CSS는 HTML에서 Header태그 안에 Style태그안에서 사용되거나 파일을 불러와서 사용되어진다.
CSS를 태그안에 적용시키는 방법은

### 1. 외부 스타일 시트(External Style Sheet)

css라는 확장자를 가진 스타일 시트 파일을 만들고 이 파일을 HTML 문서에 연결하여 사용하는 방법

```html
<head>
  <link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

장점
1. 홈페이지 전체의 스타일을 일관성있게 유지하면서
2. 변경시에 일괄적으로 변경되므로 홈페이지 제작의 효율성을 극대화 할 수 있음

단점
1. 외부 스타일 스트 파일을 계속적으로 관리해주면서 HTML 문서를 만들어 나가야 하기에 불편함
2. 외부 스타일 시트 파일이 지나치게 복잡해지면 곤란하므로 css 파일을 관리하는 노하우가 필요

### 2. 내부 스타일 시트(Internal Style Sheet)

HTML 문서내에서 <head>태그안에 스타일을 정의

```html
<head>
  <style type="text/css">
    body {
      font-size: 9px;
    }
  </style>
</head>
```

### 3. HTML 태그내에 스타일 지정(Inline Styles)

스타일을 적용하고 싶은 HTML 태그안에서 정의하는 방법

```HTML
<P style="color: gray;">이 내용은 색상이 회색으로 됩니다.</P>
```

## Selector

CSS에서 스타일을 지정하고 싶은 대상을 정할 수 있게 만들어주는 것이 Selector(선택자) 이다.
선택자는 Universal, Type, class, id, combinator, attribute, Pseudo selector가 존재합니다.

### 1. Universal Selector(전체 선택자)

```CSS
* {
  margin: 0; text-decoration: none;
}
```

전체 선택자는 HTML 페이지 내부의 모든 요소에 같은 CSS 속성을 적용합니다.
이를 사용하면 문서안의 모든 요소를 읽어내려야 하기 때문에 페이지 로딩의 속도가 느려질 수 있습니다. 사용은 지양해야합니다.


### 2. Type Selector(태그 선택자)

```HTML
/* CSS */
P {
  background: yellowgreen; color: darkgreen;
}

/* HTML */
<p>태그 선택자(이 태그만 css먹는다)</p>
<div>태그 선택자</div>
```

### 3. Class Selector(클래스 선택자)

```HTML
/* CSS */
.class1 {
  background: yellowgreen; color: darkgreen;
}
div.class2 {
  background: darkgreen; color: yellowgreen;
}

/* HTML */
<p class="class1">background: yellowgreen color: darkgreen</p>
<p class="class2">none css</p>
<div class="class2">background: darkgreen color: yellowgreen</div>
```

클래스 선택자는 주어진 값을 class속성값으로 가진 HTML요소를 찾아 선택합니다. 이때 선택하려는 속성값 앞에 마침표(***.***)를 추가하여 작성합니다.

*클래스 선택자를 사용하기 전에 생각해야 할 부분*

1. class요소 대신 사용할 수 있는 HTML 태그가 있는지 확인한다.
2. DOM트리 상단에 사용되는 클래스나 ID가 있는지 확인합니다.(DOM트리 상단에 같은 스타일을 공유하는 클래스나 ID가 있다면, 새로 클래스 선택자를 사용할 필요없이 함께 사용한다.)

### 4. ID Selector(아이디 선택자)

..


> 참조 [CSS관련](http://www.nextree.co.kr/p8468/)

## display

1. block
2. inline
3. inline-block
4. none
5. flex
6. grid

## Box Model

margin
border
padding

## mediaQuery

## SCSS

## Version

### CSS1

### CSS2

### CSS2.1

### CSS2.2
