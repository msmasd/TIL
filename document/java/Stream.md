# Java Stream

## Stream

java.util.stream이라는 객체이다.
Stream을 이용하여 주로 람다관련처리를 진행한다.

### Stream 생성 메서드

* Stream.of(): 인자값으로 실제 아이템들을 입력한다.
```java
Stream<String> stream1 = Stream.of("code", "chacha");
```

* Stream.generate(): Argument로 함수를 받는다. 함수에서 리턴되는 객체가 스트림으로 생성됩니다.
```java
Stream<String> stream2 = Stream.geneate(() -> "echo").limit(5); // 해당 함수는 무한히 호출되기 때문에 limit을 줌
```

* Stream.iterate(): generate와 유사하지만, 인자를 2개 받습니다. 첫번째는 초기값, 두번째는 함수.
```java
Stream<String> stream3 = Stream.iterate(0, n -> n * 2).limit(5);
```

* List로 생성도니 객체도 스트림으로 생성가능합니다. list에 stream() 메소드를 List를 스트림으로 생성합니다.
```java
List<String> list = Arrays.asList("a1", "a2", "a3");
Stream<String> stream6 = list.stream();
// Array객체도 가능합니다.
String[] array = new String[]("a1", "a2", "a3");
Stream stream7 = Arrays.stream(array); // Array는 Arrays.stream() 함수를 통해 스트림 객체를 생성
```

### Stream filter, Map

* fiter: Filter는 말 그대로 특정조건으로 스트림의 컨텐츠를 필터링하는 것입니다.
```java
List<String> list = Arrays.asList("a1", "a2", "c3");
Stream<String> stream1 = list.stream();
Stream<String> filtered = list.stream().filter(s -> s.startWith("a"))
filtered.forEach(System.out::print); // a1a2
```

* map: 각각의 item을 변형하여 새로운 컨텐츠를 생성하는 기능.
```java
List<String> list = Arrays.asList("a1", "a2", "c3");
Stream<String> stream2 = list.stream().map(s -> s.toUpperCase()).forEach(System.out::println); // println이므로 개행됨 A1 A2 C3
```

* FlatMap: 여러개의 스트림을 한개의 스트림으로 합쳐줍니다.
복잡합 스트림을 간단한 스트림으로 변경하는데 사용할 수 있습니다. Stream<String[]> -> Stream<String> 으로 변경가능.
```java
String[][] arrays = new String[][]{ {"a1", "a2"}, {"b1", "b2"}};
Stream<String[]> stream4 = Arrays.stream(arrays);
Stream<String> stream5 = stream4.flatMap(s -> Arrays.stream(s));
```

### Stream concat

Stream.concat(Stream A, Stream B)는 A와 B를 하나의 스트림으로 concat해준다.
```java
List<String> number = Arrays.asList("1", "2");
List<String> chars = Arrays.asList("a", "b");
Stream<String> stream = Stream.concat(number.stream(), chars.stream());
```

### Stream find

Stream에는 findFirst(), findAny()가 있습니다.
findFirst는 스트림의 순서상 가장 첫번째에 있는 것을 리턴하고
findAny는 순서와 상관없이 먼저 찾아지는 객체를 리턴합니다. // 멀티스레드일때 좀 다를수 있다.
```java
List<String> elements = Arrays.asList("a", "a1", "b", "b1", "c");
Optional<String> firstElement = element.stream()
  .filter(s -> s.startsWith("b")).findFirst(); // b
Optional<String> anyElement = element.stream()
  .filter(s -> s.startsWith("b")).findAny(); // b
Optional<String> anyElement = element.stream().paralle() // paralle이 추가되면 b1 또는 b가 리턴된다.
  .filter(s -> s.startsWith("b")).findAny(); // b
```

### Stream match

match는 스트림에서 찾고자 하는 객체가 존재하는지 탐색을 하고 boolean타입으로 결과를 리턴합니다.
메소드는 anyMatch(), allMatch(), noneMatch()가 존재.

* anyMatch(): 조건에 부합하는 객체가 1개라도 있으면 true
* allMatch(): 모든 객체가 조건에 부합해야 true
* noneMatch(): 조건에 부합하는 객체가 없어야 true

```java
List<String> element = Arrays.asList("a", "a1", "b", "b1", "c", "c1");

boolean anyMatch = element.stream().anyMatch(s -> s.startsWith("b")); // true
boolean allMatch = element.stream().allMatch(s -> s.startsWith("b")); // false
boolean noneMatch = element.stream().noneMatch(s -> s.startsWith("b")); // false
```

* findFirst(): 

## Collect

``` java
Collect(
  Suplier supplier, // 공급자: 대상 객체의 새로운 인스턴스를 만든다
  BiConsumer accumulator, // 누산자: 요소를 대상에 추가한다.
  BiConsumer combiner // 결합자: 두 객체를 하나로 병합
)
```

Collect는 Stream의 데이터를 변형 등의 처리를 하고 원하는 자료형으로 변환해줍니다.
Collect는 다음과 같은 기능들을 제공합니다.

* 스트림의 아이템들을 List 또는 Set 자료형으로 변환
* 스트림의 아이템들을 joining
* 스트림의 아이템들을 Sorting하여 가장 큰 객체 리턴
* 스트림의 아이템들의 평균 값을 리턴

### Stream의 아이템들을 HashSet으로 리턴

``` java
// Stream의 결과를 HashSet<String>로 리턴하는 코드.
Stream<String> fruits = Stream.of("banana", "apple", "mango");
HashSet<String> fruitHashSet = fruits.collect(HashSet::new, HashSet::add, HashSet::allAll);

for (String s : fruitHashSet) {
  System.out.println(s);
}

// Collectors 라이브러리를 사용한 방법
HashSet<String> fruitHashSet = fruits.collect(Collectors.toSet());
```

### Stream의 아이템들을 List 객체로 리턴

```java
Stream<String> fruits = Stream.of("banana", "apple", "mango");
List<String> fruitList = fruits.collect(Collectors.toList);
```

### Stream의 아이템들을 1개의 String 객체로 만들기

```java
Stream<String> fruits = Stream.of("banana", "apple", "mango");
// 단순히 붙인 케이스
String oneResult = fruits.collect(Collectors.joining());
System.out.println(oneResult); // bananaapllemango
// 구분자를 넣어 붙인 케이스
String resultWithSeperator = fruits.map(Object::toString).collect(Collectors.joining(", "));
System.out.println(resultWithSeperator); // banana, apple, mango
```

### 가장 큰 객체 1개만 리턴하기

```java
import static java.util.Comparator.comparing;

Stream<String> fruits = Stream.of("banana", "apple", "mango");
Function<String, Integer> getCount = fruit -> fruit.length();
Optional<String> result = fruits.map(Object::toString).collect(Collectors.maxBy(comparing(getCount)));
System.out.println("result :" + result.orElse("no item")); // result: banana
```

### Collectors로 평균 값 구하기

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4);
Double doubleResult = list.stream().collect(Collectors.averagingInt(v -> v*2));
Double result = list.stream().collect(Collectors.averagingInt(v -> v*2));
System.out.println(doubleResult) // 5.0
System.out.println(result) // 2.5
```

### Custom 객체에 Collect 적용하기

```java
Stream<Fruit> fruit2 = Stream.of(new Fruit("1", "banana"), new Fruit("2", "apple"), new Fruit("3", "mango"));
Map<String, String> map = fruit2.collect(
    Collectors.toMap(Fruit::getId, Fruit::getName));
// 동일한 key에 대한 예외처리
Map<String, String> mapNoDuplicated = fruit2.collect(
    Collectors.toMap(Fruit::getId, Fruit::getName, (existingValue, newValue) -> existingValue));
// 동일한 key이면 두개의 값을 합침
Map<String, String> mapNoDuplicated = fruit2.collect(
    Collectors.toMap(Fruit::getId, Fruit::getName, (existingValue, newValue) -> {
      String concat = existingValue + ", " + newValue;
      return concat;
      }));
for (String key : map.keySet()) {
  System.out.println("Key : " + key + ", value: " + map.get(key));
}
/*
key: 1, value: banana
key: 2, valiue: apple
key: 3, value: mango
*/

static class Fruit {
  public String id;
  public String name;

  Fruit(String id, String name) {
    this.id = id;
    this.name = name;
  }

  public String getId() { return id; }
  public String getName() { return name; }
}
```

### List의 클래스를 return값에 다른 클래스의 리스트로 받기
```java
public List<Message2> convertListClass(List<Message1> messages1) {
  return message1.stream().map(m -> new Message2(message1)).collect(Collectors.toList());
}
```

## 참고

1. https://codechacha.com/ko/java8-stream-collect/https://codechacha.com/ko/java8-stream-collect/

