# Java NIO.md

Java NIO는 New IO의 줄임말(Non-blocking IO의 줄임말이 아니다.)

> IO : NIO = Stream : Channel

## Stream vs Channel

기존의 Stream은 읽을 때와 쓸 때 `InputStream`과 `OutputStream`으로 구분해서 사용했다. Stream을 통해 흘러다니는 데이터는 기본적으로 `byte`또는 `byte[]`이고, 읽고 쓰는 작업을 지시한 후에는 그 작업이 끝나야 return 되는 blocking방식이다.


channel은 데이터가 흘러다니는 통로라는 점에서 Stream과 역활은 비슷하지만 동작 방식은 다르다.


단방향인 Stream과 달리 Channel은 양방향이라서 input/output을 구분하지 않고 그냥 `ByteChannel`, `FileChannel`를 만들어서 읽을 수도 있고, 쓸 수도 있다.


Channel은 언제나 `Buffer`를 통해서만 데이터를 읽거나 쓸 수 있다. Channel에서 데이터를 읽으면 Buffer에 담아야만 어떤 처리를 할 수 있고, Channel에 데이터를 쓰려면 먼저 Buffer에 담고, Buffer에 담긴 데이터를 Channel에 써야한다.


Channel은 non-blocking 방식도 가능하다. 다시 말하지만, **Channel을 사용하는 I/O는 언제나 non-blocking방식으로 동작하는 것이 아니라, non-blocking 방식도 가능하다는 것**이다.


| Stream | Channel |
|:---|:---|
| one-way(InputStream or OutputStream) | two-way(Channel) |
| 구현체에 따라 primitive부터 object까지 가능하나, 기본적으로는 byte 또는 byte[] | Buffer |
| Blocking | blocking or non-blocking도 가능 |

## Files.new~~()는 모두 blocking이다.

`java.nio.Files`는 NIO 중에서 File I/O를 담당하는데, 결론부터 말하면,

> 파일을 읽는데 사용되는 `Files.newBufferedReader()`, `Files.newInputStream()`등은 모두 blocking이다.
> 파일을 쓰는데 사용되는 `Files.newBufferedWriter()`, `Files.newOutputStream()`등은 모두 blocking이다.

## 다 blocking인데 File I/O에 뭐하러 NIO를 써?

### 성능

File I/O에 사용되는 Channel이 blocking모드로 동작하기는 하지만, 데이터를 `Buffer`를 통해 이동시키므로써 **기존의 Stream I/O에서 병목을 유발하는 몇가지 레이어를 건너뛸 수 있다**고 한다. (https://docs.oracle.com/javase/tutorial/essential/io/file.html)

더 구체적으로는 `Buffer`를 사용하면 DMA를 활용할 수 있다는 건데, 참고글에서 나와있다.

## File I/O에서는 정녕 Non-blocking은 없나?

있다. java 7 부터 도입되어 NIO2라고 불리는 NIO에는 AsynchronousFileChannel이 Non-blocking 모드로 동작한다. AsynchronousFileChannel은 별도의 글에서 다뤄본다.

## 참고
* https://homoefficio.github.io/2016/08/06/Java-NIO%EB%8A%94-%EC%83%9D%EA%B0%81%EB%A7%8C%ED%81%BC-non-blocking-%ED%95%98%EC%A7%80-%EC%95%8A%EB%8B%A4/
* Buffer를 사용시, DMA를 활용
  * http://eincs.com/2009/08/java-nio-bytebuffer-channel-file/
