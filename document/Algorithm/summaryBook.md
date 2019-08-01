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