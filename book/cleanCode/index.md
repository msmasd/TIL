# Clean Code

## 1 깨끗한 코드

여러 대가들의 클린코드에 대한 평

### 1.1 보이스카우트 규칙

> 캠프장은 처음 왔을 때보다 더 깨끗하게 해놓고 떠나라.

체크아웃할 때보다 좀 더 깨끗한 코드를 체크인한다면 코드는 절대 나빠지지 않는다. 한꺼번에 많은 시간과 노력을 투자해 코드를 정리할 필요가 없다. 변수 이름 하나를 개선하고, 조금 긴 함수 하나를 분할하고, 약간의 중복을 제거하고, 복잡한 if 문 하나를 정리하면 충분하다.

## 2 의미 있는 이름

### 2.1 의도를 분명히 밝혀라

변수나 함수 그리고 클래스 이름은 다음과 같은 굵직한 질문에 모두 답해야 한다.

* 변수(혹은 함수나 클래스)의 존재 이유는?
* 수행 기능은?
* 사용 방법은?

따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.

```java
public List<int[]> getThem() {
  List<int[]> list1 = new ArrayList<int[]>();
  for (int[] x : theList)
    if (x[0] == 4)
      list1.add(x);
  return list1;
}
// 지뢰찾기 게임을 만든다고 가정, theList는 게임판.
// 위의 코드를 의미있는 변수명을 사용하여 나타냄
public List<Cell> getFlaggedCells() {
  List<Cell> flaggedCells = new ArrayList<Cell>();
  for (Cell cell : gameBoard)
    if (cell.isFlagged())
      flaggedCells.add(cell);
  return flaggedCells;
}
```

### 2.2 그릇된 정보를 피하라

* 나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용해도 안된다.
* List같이 특수한 의미를 주는 단어는 정확하게 써야한다.(List가 아닌경우에, List라 쓰지말고 accountGroup, bunchOfAccounts같은 것을 사용하기)
* 서로 흡사한 이름을 사용하지 않도록 주의한다.
* 유사한 개념은 유사한 표기법을 사용한다.

### 2.3 의미 있게 구분하라

컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하지 마라(class가 있다고 klass로 구현하지말자)

* 연속된 숫자를 덧붙이거나 불용어를 추가하는 방식은 적절하지 않다.
  * a1, a2, ..., aN과 같이 숫자 붙이지 말자
  * Product라는 클래스에서 ProductInfo, ProductData와 같이 info나 data는 의미가 불분명한 불용어이다.
  * a나 the와 같은 접두어는 사용해도 좋지만, zork라는 변수가 있을때 theZork라는 변수를 추가하여 사용하지 말자.
  * ex: money vs moneyAmount, customerInfo vs customer, accountData vs account, theMessage vs message

### 2.4 발음하기 쉬운 이름을 사용하라

### 2.5 검색하기 쉬운 이름을 사용하라

MAX_CLASSES_PER_STUDENT는 grep으로 찾기가 쉽지만, 숫자 7은 은근히 까다롭다.
검색하기 쉬운 이름 관점에서는 긴 이름이 짧은 이름보다 좋다.
간단한 메서드에서 로컬 변수만 한 문자를 사용한다. **이름 길이는 범위 크기에 비례해야 한다[N5]**

```java
for (int j=0; j<34; j++) {
  s += (t[j]*4)/5;
}
/// 위보다는 아래 코드가 검색이 가능하다.
int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for (int j=0; j < NUMBER_OF_TASKS; j++) {
  int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
  int realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
  sum += realTaskWeeks;
}
```

위 코드에서 WORK_DAYS_PER_WEEK이 5인데, 그냥 5만 사용한다면 5가 들어가는 이름을 모두 찾은 후 의미를 분석해 원하는 상수를 가려내야 한다.

### 2.6 인코딩을 피하라

* 예전에는 변수명에 String같이 타입을 기재하는 경우도 있었다. (IDE가 잘 알려주지않아서 변수명으로만 확인했어야 했기 때문 그러나 요즘에는 IDE가 타입을 잘 알려준다)
* 멤버 변수 접두어는 m_ 이라는 접두어를 붙여서 사용했는데, 요즘에는 IDE가 알아서 잘 알려준다.

### 2.7 자신의 기억력을 자랑하지 마라

### 2.8 클래스 이름

### 2.9 메서드 이름

### 2.10 기발한 이름은 피하라

### 2.11 한 개념에 한 단어를 사용하라

### 2.12 말장난을 하지 마라

### 2.13 해법 영역에서 가져온 이름을 사용하라

### 2.14 문제 영역에서 가져온 이름을 사용하라

### 2.15 의미 있는 맥락을 추가하라

### 2.16 불필요한 맥락을 없애라

## 3 함수

## 4 주석

## 5 형식 맞추기

## 6 객체와 자료 구조

## 7 오류 처리

## 8 경계

## 9 단위 테스트

## 10 클래스

## 11 시스템

## 12 창발성

## 13 동시성

## 14 점진적인 개선

## 15 JUnit 들여다보기

## 16 SerialDate 리팩터링

## 17 냄새와 휴리스틱

## 18 부록A 동시성2
