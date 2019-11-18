# Java Inner Class

## Inner class(중첩 클래스)

하나의 클래스 내부에 또 다른 클래스가 내포되어 있는 상태(클래스 관리의 효율을 높임)

* 중첩되는 클래스는 하나 이상
* Outer 클래스 멤버를 Inner 클래스에서 사용 가능.
* Outer 클래스에서 Inner 클래스 멤버 사용 불가능. (사용하고 싶으면 객체를 직접 발생 시켜야 함)
* 일반 중첩 클래스 내부에서는 static과 관련된 멤버를 선언할 수 없음

```java
class Outer {
    ...
    class Inner {
        ...
    }
}

Outer outer = new Outer();
Outer.Inner inner = outer.new Inner(); // Inner 클래스 객체 생성
```

## static inner class(정적 중첩 클래스)

중첩 클래스 내부에서 static과 관련된 멤버를 선언할 수 있는 클래스

* 클래스의 이름 앞에 static을 붙임
* static 특성상 객체를 독립적으로 만들 수 있음
* static 멤버를 선언할 수 있고, static 메서드도 만들어 사용할 수 있음.
* Outer 클래스의 static 멤버만 Inner에서 사용할 수 있음

```java
class Outer {
    ...
    static class Inner {
        ...
    }
}

Outer.Inner inner = new Outer.Inner(); // Inner 클래스 객체 생성
```

## local inner class(지역 중첩 클래스)

특정 메서드에 한정적인 용도로 사용하는 클래스

* 접근 제한자와 지정 예약어를 사용할 수 없는 형태
* 일반 중첩 클래스의 형태와 유사하기 때문에 static멤버를 선언하지 못함
* 객체 생성은 외부에서 할 수 없음

```java
class Outer {
    ...
    method() {
        ...
        class Inner {
            ...
        }
    }
}

public class Exam10 {
    public static void main(String[] args) {
        final int x = 300;

        class Inner {
            int y = 400;

            public void display() {
                System.out.println("x : " + x);
                System.out.println("y : " + y);
            }
        }

        Inner inner = new Inner();
        inner.display();
    }
}
```

## anonymous inner class(익명 중첩 클래스)

지역 중첩 클래스의 변형된 형태.
기존 클래스의 특정 메서드를 오버라이딩하여 원하는 형태로 재정의하여 사용하는 방식

* class 예약어와 클래스명을 가지지 않고 단지 instance의 생성과 내용부의 정의만 가짐
* 여기에 사용되는 중첩 클래스는 이미 기존에 존재하는 클래스이어야함.
* 내부 클래스는 생성자를 작성할 수 없음.(생성자를 작성하게 되면 컴파일 에러)
* 외부 멤버 중 final만 포함

```java
class Inner {
    ...
}

class Outer {
    ...
    method() {
        ...
        new Inner() {
            ...
        }
    }
}
```

## 참고

* https://gyrfalcon.tistory.com/entry/JAVAJ-Nested-Class
