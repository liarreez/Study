# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

<br>

## 의존을 나타내는 방법

<br>

### 1. 정적 유틸리티 클래스로 구현
```java
public class Service {
    private static final Repository repository = ...;

    private Service() {}
}
```

<br>

### 2. 싱글턴으로 구현
```java
public class Service {
    private static final Repository repository = ...;

    private Service() {}
    public static Service INSTANCE = new Service(...);
}
```

1번과 2번 방법 모두 사용하는 자원에 따라 동작이 달라지는 클래스의 경우 자원을 쉽게 변경할 수 없어 적합하지 않음

<br>

### 3. 의존 객체 주입
```java
public class Service {
    private final Repository repository;

    public Service(Repository repository) {
        this.repository = repository;
    }
}
```

의존 객체 주입 변형 : 생성자에 자원 팩터리를 넘겨주는 방식
```java
// 팩터리가 생성한 타일들로 구성된 모자이크를 만드는 메서드
Mosaic create(Supplier<? extends Tile> tileFactory) {...}
```


#### 특징
 - 의존 객체 주입을 활용하면 유연성, 재사용성, 테스트 용이성이 개선됨
 - 의존성이 많을 경우 코드가 복잡해지지만 의존 객체 주입 프레임워크를 활용함으로 해결할 수 있음
