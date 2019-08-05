# 알고리즘 책 정리

## 1.3 백, 큐, 스택

### 1.3.1 API

백(Bag)

```java
public class Bag<Item> implements Iterable<Item> {
  Bag() // 공백 백 생성
  void add(Item item) // 항목 추가
  boolean isEmpty() // 백이 비어 있는가?
  int size() // 백에 든 항목의 개수
}
```

선입선출(FIFO) 큐

```java
public class Queue<Item> implements Iterable<Item> {
  Queue() // 공백 큐 생성
  void enqueue(Item item) // 항목 추가
  Item dequeue() // 최근에 추가된 항목 제거(꺼내기)
  boolean isEmpty() // 큐가 비어 있는가?
  int size() // 큐에 든 항목의 개수
}
```

후입선출(LIFO) 스택

```java
public class Stack<Item> implements Iterable<Item> {
  Stack() // 공백 백 생성
  void push(Item item) // 항목 추가
  Item pop() // 가장 마지막에 추가된 항목 제거
  boolean isEmpty() // 스택이 비어 있는가?
  int size() // 스택에 든 항목의 개수
}
```

### 제네릭

컬렉션 ADT의 가장 핵심적인 특징은 어떤 타입의 데이터든 사용할 수 있어야 한다. 이런 것을 자바의 제네릭(generic) 메커니즘이 가능하게 해준다.

제네릭은 다른 말로 파라미터화된 타입이라고 하기도 한다. 클래스 뒤에 기입된 `<Item>` 표기는 API 구현부에서 사용된 Item을 대체할 실제 타입을 의미한다.
스택을 구현하는 시점에서 Stack에 저장될 데이터 타입이 무엇이 될지 알 수가 없다. 클라이언트가 실제로 Stack을 이용하는 시점에야 Item의 데이터 타입이 결정된다. Item은 어떤 종류의 참조형 데이터 타입에도 대응될 수 있다.

### 오토박싱(Autoboxing), 오토언박싱

제네릭 타입 파라미터로부터 생성되는 변수는 참조형 타입으로만 생성되어야 한다. 이 때문에 int와 같은 기본 데이터 타입이 문제가 되는데 자바에서는 기본 데이터 타입도 제네릭 코드에서 활용할 수 있도록 특별한 메커니즘을 지원한다. 각각의 기본 데이터 타입은 대응되는 참조형 타입들이 있다.(String, Double, Integer, Long 등등) 자바는 기본 데이터 타입과 대응되는 참조형 타입을 자동으로 상호 변환해준다. 대입 구문이나 메서드 인수 산술/논리 표현식 등에서 자동으로 변환된다.
이러한 자동 변환 때문에 기본 데이터 타입을 제네릭에 활용할 수 있게 된다.
```java
Stack<Integer> stack = new Stack<Integer>();
stack.push(17); // 오토박싱 (int -> Integer)
int i = stack.pop() // 오토언박싱 (Integer -> int)
```

int가 자동으로 Integer로 변환되는것이 **오토박싱**
Integer(레퍼 타입)가 자동으로 int로 변환되는것이 **오토언박싱**

### 컬렉션 반복자

많은 수의 애플리케이션이 컬렉션의 항목들 각각에 대해 어떤 처리를 하거나 컬렉션의 모든 항목들을 순회할 수 있는 기능을 필요로 한다. 컬렉션 항목들에 대한 순회 개념은 자바를 포함한 현대의 프로그래밍 언어들이 최우선순위로 달성하고자 하는 패러다임이다. 이 매커니즘을 반복자라고 부른다. 반복자를 통해 컬렉션의 세부적인 구현 방식에 신경 쓰지 않고서도 명료하고 압축적인 코드를 작성할 수 있다.

```java
Queue<Transaction> collection = new Queue<Transaction>();
for (Transaction t : collection)
{ StdOut.println(t); }
```

이 클라이언트 코드는 컬렉션이 내부적으로 항목들을 어떻게 표현하는지 구현이 어떻게 되어 있는지 전혀 알 필요가 없다. 단지 컬렉션에 있는 항목들을 하나씩 순회하고 싶을 뿐이다.
컬렉션에서 반복자를 지원하기 위해서는 추가적인 구현상의 노력이 필요하지만 그 노력은 충분한 보상을 받는다.

### 백

백은 항목을 삭제할 수 없는 컬렉션이다.
백의 목적은 항목을 수집하고 수집된 항목들을 순회할 수 있는 도구를 제공하는 것.
항목들을 어느 순서로 순회하는지 관심이 없다.

### 선입선출(FIFO) 큐

선입선출 큐는 먼저 들어간 것이 먼저 나오는 것(First-In First-Out)을 정책으로 하는 컬렉션이다.

### 후입선출(LIFO) 스택

후입선출 스택은 마지막으로 들어간 것이 가장 먼저 나오는(Last-In First-Out)을 정책으로 하는 컬렉션이다.

#### 산술 표현식 계산을 위한 스택 알고리즘

```java
public class Evaluate
{
  public static void main(String[] args) {
    Stack<String> ops = new Stack<String>();
    Stack<Double> vals = new Stack<Double>();
    while(!StdIn.isEmpty()) {
      String s = StdIn.readString();
      if (s.equals("("))                     ;
      else if (s.equals("+"))     ops.push(s);
      else if (s.equals("-"))     ops.push(s);
      else if (s.equals("*"))     ops.push(s);
      else if (s.equals("/"))     ops.push(s);
      else if (s.equals("sqrt"))  ops.push(s);
      else if (s.equals(")")) {
        String op = ops.pop();
        double v = vals.pop();
        if (op.equals("+"))       v = vals.pop() + v;
        else if (op.equals("-"))  v = vals.pop() - v;
        else if (op.equals("*"))  v = vals.pop() * v;
        else if (op.equals("/"))  v = vals.pop() / v;
        else if (op.equals("sqrt"))  v = Math.sqrt(v);
        vals.push(v);
      }
      else vals.push(Double.parseDouble(s));
    }
    StdOut.println(vals.pop());
  }
}
```

#### 고정 크기 스택

```java
public class FixedCapacityStackOfStrings
{
  private String[] a;
  private int N;

  public FixedCapacityStackOfStrings(int cap)
  { a = new String[cap]; }

  public boolean isEmpty()  { return N == 0; }
  public int size()         { return N; }

  public void push(String item)
  { a[N++] = item; }

  public String pop()
  { return a[--N]; }
}
// 아래는 제네릭화, resize, 로이터링 방지 추가
import java.util.Iterator;
public class FixedCapacityStackOfStrings<Item> implements Iterable<Item>
{
  private Item[] a = (Item[]) new Object[1];
  private int N = 0;

  public boolean isEmpty()  { return N == 0; }
  public int size()         { return N; }

  private void resize(int max)
  {
    Item[] temp = (Itme[]) new Object[max];
    for (int i=0; i < N; i++)
      temp[i] = a[i];
    a = temp;
  }

  public void push(Item item)
  {
    if (N == a.length) resize(2*a.length);
    a[N++] = item;
  }

  public Item pop()
  {
    Item item = a[--N]
    a[N] = null // 로이터링(loitering) 방지
    if (N > 0 && N == a.length/4) resize(a.length/2);
    return item;
  }

  public Iterator<Item> iterator()
  { return new ReverseArrayIterator(); }

  private class ReverseArrayIterator implements Iterator<Item>
  {
    private int i = N;
    public boolean hasNext() { return i > 0; }
    public Item next() { return a[--i]; }
    public void remove() { }
  }
}
```

### 로이터링(loitering: 어슬렁거리기, 배회하기)

자바는 더 이상 사용되지 않는 객체를 메모리에서 해제하기 위해 가비지 컬렉션 정책을 수행한다. pop()의 구현 방식은 꺼내어진 항목을 배열에 그대로 둔다. 클라이언트가 꺼내 간 객체에 대한 참조를 끝내면 그 항목은 고아 상태가 되고 다시는 접근되지 않는다. 하지만 자바의 가비지 컬렉션 기능은 스택 배열에 남겨진 참조가 더 이상 사용되지 않는다는 것을 알 방법이 없다. 이러한 상황을(더 이상 필요 없는 객체의 참조를 유지하고 있는 상황) 로이터링 이라고 한다. 로이터링은 사용되지 않는 객체에 null을 참조하도록 하는것으로 방지가 되어진다.

### 순회 반복

컬렉션에 저장된 항목들을 하나씩 순회하는 가장 기본적인 방법은 자바의 foreach 구문으로 반복자를 루핑하는 것이다.

```java
Stack<String> collection = new Stack<String>();
...
for (String s : collection)
  StdOut.println(s);
// 위 내용은 아래와 동일하다.
Iterator<String> i = collection.iterator();
while(i.hasNext())
{
  String s = i.next();
  StdOut.println(s);
}
```

컬렉션이 반복자를 구현하는데 필요한 요소는 Iterator객체를 리턴하는 iterator() 메서드를 구현, Iterator 클래스는 boolean hasNext(), <Item> next()(컬렉션의 제네릭 항목을 리턴)를 구현해야 한다.

어떤 클래스가 반복자를 지원하기 위해서는 먼저 클래스 선언부에 implements Iterable<Item> 구문을 추가하여 Iterable 인터페이스를 따른다는 것을 표현한다.
Iterable 인터페이스의 정의이다.

```java
// 해당 인터페이스는 java.lang.Iterable에 정의되어 있다.
public interface Iterable<Item>
{
  Iterator<Item> iterator();
}

// Iterator 인터페이스 해당 인터페이스는 java.util.Iterator에 정의되어 있다.
public interface Iterator<Item>
{
  boolean hasNext();
  Item next();
  void remove();
}

// 반복자를 구현하는 클래스에서 iterator()구현
public Iterator<Item> iterator()
{ return new ReversArrayIterator(); }

public class ReversArrayIterator implements Iterator<Item>
{
  private int i = N;

  public boolean hasNext() { return i > 0; }
  public Item next() { return a[--i]; }
  public void remove() { }
}
```

## 알고리즘 접근 방법

1. 문제를 정확하게 기술하는 것(문제를 명확하게 기술하기 위해서는 기본 동작들을 추상화하는 API를 정한다.)
2. 이렇게 정의한 API들을 구현한다.
3. 데이터 구조의 특성은 알고리즘의 효율성에 직접적으로 영향을 미친다.

## 정렬

정렬은 객체들이 나열된 순서를 어떤 논리적인 순서에 맞도록 바꾸는 작업이다. 정렬을 공부하는 이유는 아래와 같은 세 가지 이유이다.

1. 정렬 알고리즘의 분석은 이 책에서 보여질 서로 다른 알고리즘 간의 비교 방법을 상세하게 소개하는 역활을 한다.
2. 정렬 알고리즘에 적용한 테크닉들은 다른 문제들에도 비슷하게 활용될 수 있다.
3. 어떤 문제를 풀 때 정렬부터 시작해야 하는 경우가 자주 있다.


### 2.1 기초적인 정렬 방법들

특정 키를 가진 항목들이 배열 안에 나열된 순서를 바꾸는 것. 모든 항목들에 대해, 어떤 항목 A의 배열 인덱스가 다른 항목 B의 배열 인덱스보다 크다면 A의 키 값도 B의 키보다 크도록 재배열하려 한다.

아래 두 메서드만을 통해서 데이터에 접근한다.

1. less(): 항목들을 비교하는 함수
2. exch(): 항목을 교환하는 함수

인증은 isSorted() 메서드를 통해서 처리한다.

#### 선택정렬

선택 정렬은 위치가 정해져있고 이 위치에 어떤 원소를 넣을지 선택하는 것

1. 배열 안에서 가장 작은 항목을 찾음
2. 그 항목을 배열의 가장 첫 항목과 자리바꿈
3. 그리고 다음번 가장 작은 항목을 찾아 두 번째 항목과 자리바꿈

#### 삽입정렬

삽입 정렬은 매 순서마다 해당 원소를 삽입할 수 있는 위치를 찾아 해당 위치에 넣는다고 이해하면 쉽습니다.

1. 항목을 삽입할 공간을 만들기 위해 삽입할 항목보다 큰 항목들은 전부 오른쪽으로 밀어서 이동시킨다.
2. 확보된 빈 공간에 항목을 삽입한다.

삽입정렬은 부분적으로 정렬된 배열에 적용될 때 최적인 알고리즘이다.

#### 삽입정렬과 선택정렬의 차이

선택 정렬은 위치가 정해져있고 이 위치에 어떤 원소를 넣을지 선택하는 것이었다면, 삽입 정렬은 원소는 정해져있고 이 원소를 어디에 넣을지 선택하는 것이라고 이해하면 될 것 같습니다

#### 알고리즘 비교

알고리즘을 비교할 때 다음의 과정을 거친다.

* 알고리즘을 구현하고 디버깅한다.
* 기본 속성들을 분석한다.
* 상대적인 성능에 대해서 가설을 세운다.
* 가설을 검증하기 위한 실험을 수행한다.

#### 쉘 정렬

삽입 정렬에 기반을 둔 빠른 알고리즘.

삽입정렬은 크기가 큰 정렬되지 않은 배열을 처리하는데 느리다. -> 인접한 항목과의 교환만 일어나기 때문에 항목들이 한 번에 한 위치로만 이동될 수 있다.

쉘 정렬은 삽입 정렬의 확장 버전으로, 서로 멀리 떨어진 항목 간에도 교환이 일어날 수 있게 함으로써 삽입 정렬이 빠르게 처리할 수 있는 부분적으로 정렬된 배열을 만든다.

매 h번째 항목들 간에 순서를 따질 때 정렬된 상태가 되도록 배열을 재정리하는것.

### 2.2 병합정렬(mergeSort)

병합은 두 개의 정렬된 배열을 하나의 큰 정렬된 배열로 합치는 작업이다. 이 작업은 단순한 재귀적 방법으로 쉽게 구현된다.
즉, 배열을 정렬할 때, 그것을 반으로 나누어 각각의 절반에 대해서 재귀적으로 정렬을 수행하고, 다시 재귀적으로 정렬 결과를 병합한다.
병합정렬의 가장 큰 장점은 실행시간이 NlogN에 비례한다. 대신 N에 비례하는 추가적인 메모리 공간을 소요한다.

#### 즉석(in-place) 병합의 추상화

병합을 쉽게 구현하는 방법은 병합 결과를 담을 새로운 배열을 두고, 중복 항목이 없는 두 개의 정렬된 배열을 입력으로 하여 병합해 넣는 것이다. 이때 배열 항목들은 Comparable 객체들이라고 가정한다. 두 입력 배열을 모두 담을 수 있을 만한 크기의 출력 배열을 준비한 후 두 입력 배열의 앞에서부터 둘 중 가장 작은 항목을 연속해서 뽑아 출력 배열에 앞에서부터 추가한다.

그런데 매우 큰 배열을 대상으로 병합 정렬을 할 때는 출력 배열을 매번 새로 생성하는 비용이 문제가 된다. 가능하다면 원본 배열 안에서 즉석으로 병합하는 것이 훨씬 바람직하다. -> 추가적인 메모리를 사용하지 않고, 각각의 절반 크기 배열을 그 안에서 정렬하고 정렬된 두 개의 절반 부분들을 같은 배열 안에서 병합할 수 있어야한다.

merge(a, lo, mid, hi)를 정의: 이 메서드는 입력되는 매열의 구간 a[lo...mid]와 a[mid+1...hi]에 대해서 병합 정렬을 수행하여 하나의 정렬된 배열 a[lo...hi]를 남긴다.

#### 하양식(top-down) 병합 정렬

즉석 병합 추상화 메서드에 기반하여 재귀적으로 구현.
병합 정렬의 재귀적인 구현은 "분할 정복"이라는 알고리즘 설계 패러다임의 프로토타입과도 같다. 분할 정복 패러다임은 문제를 잘게 쪼개서 각각의 부분 문제들을 해결한 후, 그 결과들을 이용해 다시 전체 문제를 푸는 접근 방법이다.

#### 성능 향상이 가능한 구현들

##### 1. 작은 부분 배열에 삽입 정렬 이용하기

재귀적인 알고리즘들은 작은 케이스들에 대해서 별도의 방식을 적용해서 성능을 개선할 수 있다. 재귀적이라는 구조 자체가 작은 케이스를 많이 처리하는 특성을 가지기 때문에 작은 케이스들에 대한 개선이 전체 알고리짐에 대한 개선과 직결된다. 정렬 자체에 있어서는 삽입 정렬(또는 선택 정렬)이 더 단순하기 때문에 매우 작은 배열에 대해서는 병합 정렬보다 더 빠를 가능성이 크다.

##### 2. 배열이 이미 정렬된 상태인지 확인하기

이미 정렬된 상태로 입력되는 배열에 대한 중복 정렬을 생략한다면 정렬된 배열에 대한 성능을 선형으로 만들 수 있다. a[mid]가 a[mid+1]보다 작거나 같은지 확인하여 merge()의 호츌을 생략하도록 수정한다.

##### 3. 임시 작업 배열로의 복사 제거

병합용 임시 작업 배열로의 복제에 드는 시간을(공간이 아니라) 제거하는 것도 가능하다. 이를 위해 sort메서드의 호출 두 개를 이용한다. 하나는 원본 배열을 입력으로 받아 정렬 결과를 임시 배열에 넣고, 다른 하나는 그 임시 배열을 입력으로 받아 정렬 결과를 원본 배열에 넣는다.

#### 상향식(botton-up) 병합 정렬

일괄 병합 작업을 전체 배열이 정렬될 때까지 반복한다. 이 방법은 재귀적인 방법에 비해 코드 양이 더 적기도 하다. 먼저 1:1 병합 단계부터 시작한다.(1:1은 크기가 1인 부분 배열)
그 다음 2:2 병합, 4:4 병합,... 계속 해 나간다. 다음 단계의 부분 배열이 이전 단계의 마지막 자투리 부분 배열보다 크기가 작은 역전 현상이 있을 수 있다.(이것이 merge()를 수행하는데 문제가 되지는 않는다)
그 외의 경우에는 같은 크기의 부분 배열 간에 병합이 각 단계마다 그 크기를 두 배씩 증가시키면서 일어난다.

#### 정렬의 복잡도

복잡도를 구하는 첫 번째 단계는 계산 모델을 세우는 것.
보통 문제에 적합한 가장 단순한 모델에 대한 이해부터 시도한다. 정렬에 있어서는 비교 연산을 기본으로 하는 알고리즘 클래스를 살펴본다. 이 클래스에 속한 알고리즘들은 항목들의 키 값을 비교하는 연산에만 의존하여 의사결정을 내린다. 비교 기반 알고리즘은 비교 연산 간에 아무리 많은 계산도 순식간에 할 수 있는 것으로 가정한다. 단, 어떤 키에 대한 정보를 얻을 때 다른 키와 비교하는 방법 외에는 아무런 정보도 얻을 수 없는 것으로 한다. Comparable API를 사용해야 하는 제약 때문에 이 장에서 다루는 알고리즘들은 모두 비교 기반 알고리즘 클래스에 속한다.

### 2.3 퀵정렬

추가적인 메모리를 사용하지 않고 입력 배열의 메모리 안에서 즉석으로 정렬되고(작은 크기의 스택 메모리만 추가로 요구한다.) 평균적으로 크기 N인 배열을 정렬하는데 NlogN에 비례하는 시간을 소요한다. 구현도 매우 간단하지만, 부주의한 구현으로 기대 이하의 성능을 보이기 쉽다는 점이다.

#### 기본 알고리즘

분할 정복 방식으로 정렬을 수행한다. 배열을 부분 배열로 분할한 다음 각각의 부분 배열을 독립적으로 정렬한다.
병합 정렬에서는 배열을 두 개의 부분 배열로 나누어 각각을 정렬한 다음 정렬된 부분 배열들을 다시 전체 배열로 합치며 정렬한다.
퀵 정렬은 두 배열을 나뉘되, 각각의 배열이 정렬된다면 전체 배열도 이미 정렬된 상태가 되도록 나눈다.
병합 정렬에서는 전체 배열에 대한 작업을 수행하기 이전에 두 개의 재귀 호출을 했지만, 퀵 정렬에서는 전체 배열에 대한 작업을 수행한 이후에 두 개의 재귀 호출을 한다.
병합 정렬은 배열의 절반이 나눠지지만, 퀵 정렬은 배열의 내용에 따라서 분할되는 위치가 달라진다.

#### 특징

즉석(in-place) 분할: 추가적인 작업용 배열을 만들지 않고 origin 배열을 분할한다.
경계선 넘지 않기: 만약 분할 기준 항목이 가장 작은 키 또는 가장 큰 키를 가졌다면 스캔 중에 배열의 왼쪽 또는 오른쪽 끝을 넘어가지 않도록 조심해야한다.
루프의 종료: 어떤 루프든 반드시 종료 조건을 가져야한다.
분할 기준 항목과 동일한 키를 가지는 배열 항목 다루기: 좌측을 스캔할 때는 분할 기준 항목보다 크거나 같은 항목이 나오면 스캔을 중단, 우측에서는 분할 기준 항목보다 작거나 같으면 중단.

분할이 균형 있게 이루어지지 않을 경우 극단적으로 비효율적이 될 수 있다. 분할이 첫번째에서 되고, 그다음이 두번째, 이런식으로 되면 N^2이 된다.(그래서 무작위성을 중요시함)
