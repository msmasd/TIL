# Slf4j

자바에는 무수히 많은 로깅관련 모듈이 존재합니다.
전통적인 apache commons logging, log4j 그리고 많이 사용되어지는 logback이나 log4j2등이 있다.
최근에는 logback이나 log4j2를 권장합니다.

만일 log4j를 걷어내고 logback으로 교체하는 업무가 주어졌을때, maven에서 log4j의 dependency를 exclude하고 다시 logback을 추가합니다.
이러면 IDE에 에러가 엄청나게 많이 나옵니다.

전체 교체를 하였을때, 가능하지만 commit에 대한 건은 엄청나게 발생합니다. 이렇게 되면 다른 소스에 conflict가 날 수 있는 상황이 발생할 수 있습니다.
위와 같이 교체를 용이하게 하기 위한것이, Simple Logging Facade for Java(SLF4J)는 java의 로깅 모듈들의 추상체라고 보면 됩니다.

## 왜 사용해야하는가

slf4j는 java 어플리케이션을 만들면서 왜 interface를 써야하는가와 이유가 동일합니다.
우리는 실제 코드상에서는 slf4j의 interface 코드를 사용하고 실제 로깅을 하는 구현체는 추가로 참조한 라이브러리에서 구현됩니다.

실제 코드

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

// ...
Logger logger = LoggerFactory.getLogger(this.getClass());
// ...
log.info("log!");
```

## 타 모듈 연동

많은 java logging 모듈들은 slf4j의 브릿지를 이미 제공해주고 있습니다.
브릿지는 mvnrepository에서 찾아서 넣기만 하면 됩니다.
logback을 쓰고 싶으면 slf4j-api를
log4j2를 쓰고 싶으면 log4j-slf4j-impl과 log4j-api를 추가하면 됩니다.


## 참고

* https://inyl.github.io/programming/2017/05/05/slf4j.html
