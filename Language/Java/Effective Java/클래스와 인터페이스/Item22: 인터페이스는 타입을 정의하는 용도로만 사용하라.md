# 인터페이스는 타입을 정의하는 용도로만 사용하라

<br>

> 인터페이스를 상수 공개용 수단과 같은 다른 용도로 사용하면 안됨

<br>

## 인터페이스의 용도

---

> 타입을 정의함으로 클라이언트가 인스턴스로 무엇을 할 수 있는지 알림

<br>

## 인터페이스의 오용 - 상수 인터페이스

---

```java
// 상수 인터페이스
public interface Constants {
    String HELLO = "Hello";
    String WORLD = "World";
}

// 상수 인터페이스 구현
public class MyClass implements Constants {
    public void greet() {
        System.out.println(HELLO + " " + WORLD);
    }
}
```

- 내부 구현(상수 필드) 인터페이스로 외부에 노출 되는 문제 발생
- 바이너리 호환성(변경 후에도 기존 바이너리가 제대로 작동됨)을 위해 항상 상수 필드를 구현하고 있어야 함
- final이 아닌 클래스의 경우 구현체들이 모두 인터페이스가 정의한 상수에 영향을 받음

<br>

## 상수를 공개하는 다른 방법

---

 - 연관된 클래스와 인터페이스 자체에 추가(Integer.MAX_VALUE)
   ```java
   public class MyClass {
       private static final String HELLO = "Hello";
       private static final String WORLD = "World";

       public void greet() {
           System.out.println(HELLO + " " + WORLD);
       }
   }
   ```
 - 열거타입(enum) 사용
 - 인스턴스 할 수 없는 유틸리티 클래스 사용(Math.PI)
   
   ```java
    public final class Constants {
        public static final String HELLO = "Hello";
        public static final String WORLD = "World";

        // private 생성자로 인스턴스화 방지
        private Constants() {
            throw new AssertionError("Cannot instantiate constants class");
        }
    }
   ```


