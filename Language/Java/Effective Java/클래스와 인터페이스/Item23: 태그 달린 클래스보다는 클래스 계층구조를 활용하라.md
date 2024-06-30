# 태그 달린 클래스보다는 클래스 계층구조를 활용하라

<br>

## 계층 달린 클래스

---

```java
public class Shape {
    enum ShapeType { CIRCLE, RECTANGLE }

    // 태그 필드
    private final ShapeType type;

    // CIRCLE에 필요한 필드
    private final double radius;

    // RECTANGLE에 필요한 필드
    private final double length;
    private final double width;

    // 생성자 (CIRCLE)
    public Shape(double radius) {
        this.type = ShapeType.CIRCLE;
        this.radius = radius;
        this.length = 0;
        this.width = 0;
    }

    // 생성자 (RECTANGLE)
    public Shape(double length, double width) {
        this.type = ShapeType.RECTANGLE;
        this.length = length;
        this.width = width;
        this.radius = 0;
    }

    // 면적 계산 메서드
    public double area() {
        switch (type) {
            case CIRCLE:
                return Math.PI * radius * radius;
            case RECTANGLE:
                return length * width;
            default:
                throw new AssertionError(type);
        }
    }
}
```

 - 불필요한 코드가 많고 가독성이 떨어짐
 - 사용하지 않는 코드들도 항상 같이 사용하여 메모리 많이 사용
 - final 필드 사용을 위해 사용하지 않는 필드도 모두 초기화
 - 인스턴스의 타입만으로 의미가 불분명
 - 유지보수시 변경할 부분이 많음(정사각형을 추가한다고 생각해보자)


<br>

## 클래스 계층 구조

---

### 클래스 계층 구조 만드는 방법
 - 계층구조의 루트가 될 추상 클래스 정의
 - 공통으로 사용되는 필드와 메서드를 루트 클래스에 구현
 - 동작이 달라지는 메서드는 루트 클래스의 추상 메서드로 선언
 - 동작이 달라지는 메서드는 하위 클래스에서 구현
 - 하위 클래스에서만 사용하는 필드를 정의

```java
// 상위 클래스 또는 인터페이스
public abstract class Shape {
    public abstract double area();
}

// Circle 클래스
public class Circle extends Shape {
    private final double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

// Rectangle 클래스
public class Rectangle extends Shape {
    private final double length;
    private final double width;

    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    public double area() {
        return length * width;
    }
}

```

> 태그 달린 클래스의 모든 단점이 해결
