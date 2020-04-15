# 토비_Reactive_Streams

## Reactive Stream 설명

* 에릭마이어(Reactive Stream 개발자)
  * Duality
    * 쌍대성

## iterable and for

```java
public static void main(String[] args) {
  List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
  Iterable<Integer> iter = Arrays.asList(1, 2, 3, 4, 5);
  // List는 Iterable의 서브 클래스이다. -> List는 Iterable을 구현한 클래스.
    // List -> Collections -> Iterable
  // Iterable 자바 문서를 보면, 해당 인터페이스를 사용하여 구현을 하면, for-each loop의 대상이 될 수 있어야 한다고 나와있다.
  // for-each loop
  for(Integer i : list) {
    System.out.println(i);
  }
  for(Integer i : iter) {
    System.out.println(i);
  }

  // 여기서 list를 10의 인수를 돌리고 싶을때, Arrays.asList(1, 2, 3, ... 10) 까지 쓸 필요 없이 Iterable인터페이스를 구현하여 돌려보자.
  // 익명 클래스로 구현
  Iterable<Integer> iter1 = new Iterable<>() {
    @Override
    public Iterator<Integer> iterator() {
      return null;
    }
  };

  // 익명 클래스를 구현 시에, 함수가 하나인 경우를 구현해야 한다면 람다로 구현해도 된다.
  // 익명 클래스 -> 람다 변경방법
  /*
   * 1. 인터페이스 이름 삭제
   * 2. public 삭제
   * 3. 메소드 이름도 삭제
   * 4. 리턴타입도 삭제
   * 5. 파라미터가 들어가는것과 바디만 필요.
   * 6. () -> {} 방식으로 변경
   * 7. (optional) 만약 바디 라인이 한줄이면, 중괄호({}), return 생략
  */

  // 일반적으로는 이러한 코드를 콜드소스라고 한다.
  Iterable<Integer> iter1 = () ->
    new Iterator<>() {
      static final int i = 0;
      static final int MAX = 10;

      public boolean hasNext() {
        return i < MAX;
      }

      public Integer next() {
        return ++i;
      }
  };
  // for-each loop는 위에 있는 hasNext와 next로 동작한다.
  for (Integer i : iter1) {
    System.out.println(i);
  }

  // for문은 사실 while으로 컴파일된다.
  for(;;)
  for(Iterator<Integer> it = iter.iterator(); it.hasNext();) {
    System.out.println(it.next());
  }
}

  // Iterable <---> Observable (duality) // 기능은 똑같은데, 대칭되어서 하는것.
  // Pull           Push
  // for는 데이터를 pull해오는 방식
  // Pull해오는 부분 -> int i = next() -> 리턴값이 있고, 인자값이 없다.

  // Observable은 notifyObservers(i) -> 리턴값이 없고, 인자값이 있다.
  // 위와같이 방향이 완전히 반대로 되어있는 것이 쌍대성(duality)
```

## Observer pattern

* 자바 안에는 Observable이라는 클래스가 있다.
  * 하지만, Reactive Stream이 들어오고 나서는 deprecated되어있다.

```java
public class IntObservable extends Observable implements Runnable {
  // 데이터를 만들어 내야 한다. -> runnable로 데이터 만들어낸다.
  @Override
  // GOF에서는 subject, ReactiveStreams에서는 publisher
  public void run() {
    for(int i = 1; i<= 10; i++) {
      setChanged(); // 먼저 이전과 다른 새로운 데이터가 생겼다는 것을 setChanged메소드를 호출
      notifiyObservers(i); // 데이터를 notify를 통해서 보낸다. // push
      // int i = it.next()  // pull
    }
  }
}

public static void main(String[] args) {
  Observer ob = new Observer() {
    @Override
    // GOF에서는 observer, ReactiveStreams에서는 subscriber
    // Observable에서 notifyObservers이 호출되면 각 옵저버에 있는 update 메소드가 동작한다.
    public void update(Observable o, Object arg) {
      System.out.println(arg);
    }
  }

  IntObservable io = new IntObservable();
  io.addObserver(ob); // observer 등록

  io.run(); // 데이터 만들어냄
}

public static void main(String[] args) {
  Observer ob = new Observer() {
    @Override
    // GOF에서는 observer, ReactiveStreams에서는 subscriber
    // Observable에서 notifyObservers이 호출되면 각 옵저버에 있는 update 메소드가 동작한다.
    public void update(Observable o, Object arg) {
      System.out.println(arg);
    }
  }

  IntObservable io = new IntObservable();
  io.addObserver(ob); // observer 등록

  ExecutorService es = Executors.newSingleThreadExecutor();

  es.execute(io.run());
}
```

```java
Observable
// 옵저버 추가
// notify
// Source -> 얘가 Event를 던짐 / Data (target)-> Observer
// 옵저버는 옵저버블에게 등록이 되어 이벤트를 받는다.(notify)
// 옵저버의 특징은 여러개가 될 수 있다.
// 미리 딱 정해져있는 데이터를 가져오는것이 아니라 데이터를 받아오기 때문에 할수 있는 일이 많다.
```

## Reactive Streams

Reactive Streams 는 표준이다.
여러 회사들이 Reactive Stream에 대한 표준을 정하고 스펙을 정하고 가이드를 정한 것 -> 해당 기술은 Java9에 API로 들어감

### Reactive Streams 주요 API

