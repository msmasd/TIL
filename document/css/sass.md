# SASS

Sass(Syntactically Awesome Style Sheets)는 CSS를 만들어주는 언어입니다.
javascript처럼 특정 속성(ex. color, margin..)의 값(ex. #000, 3px..)을 변수로 선언하여 필요한 곳에 선언된 변수를 적용할 수도 있고, 반복되는 코드를 한번의 선언으로 여러 곳에서 재사용할 수 있도록 도와주는 기능이 있다.

## SASS VS SCSS

SCSS(Superset CSS)는 CSS 구문과 완전히 호환되도록 새로운 구문을 도입해 만든 CSS의 상위집합입니다.

SASS기법에서는 셀럭터나 클래스 뒤에 괄호({})를 사용하지 않고 변수뒤에 세미콜론(;)도 사용하지 않는다. 인덴트를 이용한 간략한 기법이 특징.
SCSS는 네스트 형힉의 기법으로 CSS작성 방법은 CSS와 거의 차이가 없다.

```Scss
$red : #FF0000
.hoge
  background-color : $red
```
위의 코드는 Sass방식


```Scss
$red : #FF0000;
.hoge {
  color: $red;
}
```
위의 코드는 Scss방식

### 1.1 SCSS의 장단점

> 장점

* 표기가 CSS와 닮아 있음
* 네스트 표시가 되어있어서 알기쉬움
* @mixin와 @include등의 기법을 사용하지 않아서 알기 쉬움
* 주류여서 인터넷에 정보가 많음

> 단점

* 세미콜론과 괄호, @mixin과 @include등 입력해야할 요소가 많음
* 처음에는 알기 쉬운데 프로젝트가 커지면 커질수록 코드가 길어서 보기 어려워짐
* 괄호를 사용하므로 괄호분 인덴트를 맞추는게 귀찮음

### 1.2 SASS의 장단점

> 장점

* 괄호를 사용하지 않으므로 코드가 그만큼 짦아짐
* @mixin과 @include등은 기호로 표시하므로 입력이 편함
* 괄호를 사용하지 않으므로 인덴트 맞추는게 편함
* Pug(Jade)사용할 경우 상성이 좋음

> 단점

* 기호와 인덴트를 사용하므로 처음에 적응하기 어려움
* 인터넷에 정보가 적은편

## 2. Scss의 문법

### 2.1 mixin

자바스크립트의 function처럼 사용할 수 있는 Sass 문법입니다. 인자 값을 넘길 수도 있고, 각 인자마다 기본값을 줄 수도 있습니다.

`선언`
```Scss
@mixin fontSizeBgColor($fontSize: 40px, $bgColor: #fff) {
  font-size : $fontSize;
  background-color : $bgColor;
}
```

`사용`
```Scss
#box {
  @include fontSizeBgColor(30px, #000);
}
```

### 2.2 %와 @extend

extend는 속성 이름과 값이 완전히 동일한 소스를 재사용할 떄 사용하는 문법입니다.

`선언`
```Scss
%boxShape {
  border-radius: 10px;
  border: 2px solid black;
}
```

`사용`
```Scss
#box {
  @extend %boxShape;
}
```

### 2.3  partial 과 @import

partial은 자주 사용되는 mixin을 모아둔 파일입니다. 파일 이름은 언더스코어(_)로 시작합니다. 참고로 .scss 파일명을 언더스코어(_)로 시작하면 Sass 컴파일러는 해당 파일을 컴파일 하지 않습니다.

`선언`
```Scss
// 파일명: _mixins.scss

@mixin fontSizeBgColor1($fontSize, $BgColor) {
  ...
}

@mixin fontSizeBgColor2($fontSize, $Bgcolor) {
  ...
}
```

`사용`
```Scss
// 파일명: test.scss

@import "mixins";
@import "partial/mixins";

#box1 {
  @include fontSizeBgColor1(30px, #000);
}

#box2 {
  @include fontSizeBgColor2(20px, #fff);
}
```