# Stream

자바 8에서 추가된 스트림은 람다를 활용할 수 있는 기술 중 하나입니다.
기존에는 배열 또는 컬렉션 인스턴스를 다루는 방법은 for 또는 foreach문을 돌면서 요소 하나씩을 꺼내서 다루는 방법이었습니다.

스트림은 ‘데이터의 흐름’입니다.
배열 또는 컬렉션 인스턴스에 함수 여러 개를 조합해서 원하는 결과를 필터링 하고 가공된 결과를 얻을 수 있습니다.
또한 람다를 이용해서 코드의 양을 줄이고 간결하게 표현할 수 있습니다.
즉, 배열과 컬렉션을 함수형으로 처리할 수 있습니다.

또 하나의 장점은 간단하게 병렬처리가 가능하다는 점입니다.
하나의 작업을 둘 이상의 작업으로 잘게 나눠서 동시에 진행하는 것을 병렬처리라고 합니다.

## 스트림의 내용

생성하기: 스트림 인스턴스 생성
가공하기: 필터링 및 맵핑 등 원하는 결과를 만들어가는 중간 작업
결과 만들기: 최종적으로 결과를 만들어내는 작업

## 생성하기

보통 배열과 컬렉션을 이용해서 스트림을 만들지만 이 외에도 다양한 방법으로 스트림을 만들 수 있습니다.

### 배열 스트림

스트림은 배열 또는 컬렉션 인스턴스를 이용해서 생성할 수 있습니다.
배열은 다음과 같이 Array.stream 메소드를 사용합니다.

‘’’java
String[] arr = new String[]{“a”, “b”, “c”};
Stream<String> stream = Array.stream(arr);
Stream<String> streamOfArrayPart = Array.stream(arr, 1, 3); // 1~2 요소 [b, c]
‘’'

### 컬렉션 스트림

컬렉션 타입(Collection, List, Set)의 경우 인터페이스에 추가된 디폴트 메소드 stream을 이용해서 스트림을 만들 수 있습니다.

 ‘’’java
List<String> list = Arrays.asList(“a”, “b”, “c”);
Stream<String> stream = list.stream();
Stream<String> paralleStream = list.parallelStream(); // 병렬 처리 스트림
‘’'

### 비어있는 스트림

비어있는 스트림도 생성할 수 있습니다. 빈 스트림은 요소가 없을 때 null대신 사용할 수 있습니다.
‘’’java
public Stream<String> streamOf(List<String> list) {
	return list == null || list.isEmpty()
		? Stream.empty()
		: list.stream();
}
‘’'

### Stream.builder()

빌더를 사용하면 스트림에 직접적으로 원하는 값을 넣을 수 있습니다. 마지막에 build 메소드로 스트림을 리턴합니다.

‘’’java
Stream<String> builderStream =
	Stream.<String>builder()
		.add(“Eric”).add(“Elena”).add(“java”)
		.build(); // [Eric, Elena, Java]
‘’'

### Stream.generate()

generate 메소드를 이용하면 Supplier<T>에 해당하는 람다로 값을 넣을 수 있습니다.
Supplier<T>는 인자는 없고 리턴값만 있는 함수형 인터페이스다.
람다에서 리턴하는 값이 들어갑니다.

‘’’java
public static<T> Stream<T> generate(Supplier<T> s) { … }
Stream<String> generatedStream = Stream.generate(() -> “gen”).limit(5); // [gen, gen, gen, gen, gen]
‘''

## 참고

https://futurecreator.github.io/2018/08/26/java-8-streams/




