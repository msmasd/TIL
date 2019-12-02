# Junit5

## 어노테이션

### 1. ExtendWith

* Junit5에 추가된 어노테이션
* 이름 그대로 뭔가를 확장 시킬수 있는 어노테이션
  * value에는 Extenstion을 상속한 클래스만 가능하다.
  * 여기서 중요한것은 `ParameterResolver`이다.
    * `ParameterResolver`인터페이스는 파라미터를 컨트롤 할 수 있는 인터페이스
    * Junit4에서는 무조건 기본 생성자가 있어야 하며, `test`메서드는 파라미터가 없어야 했다.
    * Junit5부터는 기본생성자뿐만 아니라 `test`메서드에도 파라미터가 있어도 된다.
    * 하지만 그에 맞게 구현을 해야한다. 이것을 해주는것이 `ParameterResolver` 인터페이스이다.

```java
public interface ParameterResolver extends Extension {
    boolean supportParamter(ParameterContext parameterContext, ExtensionContext extensionContext)
        throws ParameterResolutionException;
    Object resolveParameter(ParameterContext parameterContext, ExtensionContext extensionContext)
        throws ParameterResolutionException;
}
```
`ParameterResolver` 인터페이스는 다음과 같은 형태이다.
* `supportParameter` 지원가능한 파라미터 타입을 검사하고
* `resolveParameter` 메서드는 실제 지원가능한 타입을 조작하는 메서드이다.

..?

## 참고
* http://wonwoo.ml/index.php/post/1878
