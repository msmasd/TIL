# Reflection

* 자바 언어의 기능 중 하나로 프로그램 내부 속성을 조작할 수 있게 합니다.
  * e.g. java 클래스가 가지고 있는 모든 멤버의 이름을 가져와서 표시할 수 있습니다.

## 간단한 리플릭션 예제

String 클래스의 풀패스를 통해 String이 가지고 있는 모든 메소드를 출력하는 간단한 예제

```java
try {
    Class c = Class.forName("java.lang.String");
    Method m[] = c.getDeclaredMethods();
    for (int i = 0; i < m.length; i++)
        System.out.println(m[i].toString());
} catch (Throwable e) {
    System.err.println(e);
}
```

## 클래스의 메소드 찾기

리플렉션의 가장 기본적이고 가장 중요한 사용법 중 하나가 바로 클래스에 정의된 메소드 찾기 입니다.
메소드를 찾는 것 뿐만 아니라 메소드가 가지고 있는 파라미터 타입, Exception 타입, 반환 타입 등을 알아 낼 수 있습니다.

```java
try {
    Class cls = Class.forName("java.lang.String");
    Method methods[] = cls.getDeclaredMethods();
    for (int i=0; i< methods.length; i++) {
        Method m = methods[i];
        Log.e(TAG, "메소드 이름 = " + m.getName());
        Log.e(TAG, "정의된 클래스 이름 = " + m.getDeclaringClass());

        Class pvec[] = m.getParameterTypes();
        for (int j = 0; j < pvec.length; j++) {
            Log.e(TAG, "인자 #" + j + " " + pved[j]);
        }

        Class evec[] = e.getExceptionTypes();
        for (int j = 0; j < evec.length; j++) {
            Log.e(TAG, "에러 #" + j + " " + evec[j]);
        }

        Log.e(TAG, "return type = " + m.getReturnType());
        Log.e(TAG, "----");
    }

} catch (Throwable e) {
    Log.e(TAG, e.toString());
}
```



## 참고
* https://www.charlezz.com/?p=756
* https://gyrfalcon.tistory.com/entry/Java-Reflection
