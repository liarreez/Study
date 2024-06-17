# private 생성자나 열거 타입으로 싱글턴임을 보증하라

<br>

## 싱글턴

---

> 인스턴스를 오직 하나만 생성할 수 있는 클래스

- 단점 : 싱글턴 인스턴스를 가짜(mock) 구현으로 대체할 수 없어 테스트하기 어려움

<br>

### 싱글턴 방식

#### 1) 생성자는 private로 감추고, public static 멤버가 final 필드인 방식
```java
public class Singleton {
    public static final Singleton INSTANCE = new Singleton();

    private Singleton() {

    }
}
```

##### 장점
 - 간단하고 싱글턴임이 명확히 드러남
##### 단점
 - 지연 초기화와 같은 추가 기능 적용 불가
 - 직렬화, 역직렬화 시 새로운 인스턴스가 만들어지는 것을 readResolve 메서드를 활용해서 예방해야 함
 - 리플렉션 공격을 예방하기 위해 두 번째 객체가 생성되려 할 때 예외를 던져야 함

#### 2) 생성자는 private로 감추고, 정적 팩터리 메서드를 public static 멤버로 제공
```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

##### 장점
 - API 변경 없이 싱글턴이 아니게 변경 가능
 - 정적 팩터리를 제네릭 싱글 팩터리로 만들 수 있음
 - 정적 팩터리의 메서드 참조를 공급자(supplier)로서 사용 가능
 - 지연 초기화와 같은 추가 기능 적용 가능
##### 단점
 - 복잡성 증가
 - 직렬화, 역직렬화 시 새로운 인스턴스가 만들어지는 것을 readResolve 메서드를 활용해서 예방해야 함
 - 리플렉션 공격을 예방하기 위해 두 번째 객체가 생성되려 할 때 예외를 던져야 함

<br>

 #### 3) 원소가 하나인 열거타입을 선언 - 가장 추천하는 방식
 ```java
public enum Singleton {
    INSTANCE;

    public void someMethod() {
    }
}
 ```
##### 장점
 - 간단하고 명확함
 - 직렬화 상황 및 리플렉션 공격에 대해 추가적인 예방이 필요 없음
##### 단점
 - 클래스 상속을 할 수 없음
 - Enum이 지원되는 자바 5 이상에서만 사용 가능
