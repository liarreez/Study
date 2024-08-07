# 익명 클래스보다는 람다를 사용하라

> 가능하다면 익명 클래스보다 가독성이 좋은 람다를 사용하자

<br>

## 익명 클래스

---

> 이름이 없는 내부 클래스

<br>

ex1)
```java
PriorityQueue<Integer> pq = new PriorityQueue<>(new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2 - o1; // 내림차순 정렬
    }
});
```

ex2)
```java
public enum Operation {
    PLUS {
        @Override
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS {
        @Override
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES {
        @Override
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE {
        @Override
        public double apply(double x, double y) {
            return x / y;
        }
    };

    public abstract double apply(double x, double y);
}

```

간단한 구현도 코드가 길어져 가독성이 떨어진다

<br>

### 익명 클래스를 사용해야 하는 경우(람다로 대체 못하는 경우)
- 추상 클래스의 인스턴스를 생성할 때
- 추상 메서드가 여러 개인 인터페이스의 인스턴스를 만들 때
- 함수 객체가 자신을 참조해야 할 때

<br>

=> 익명 클래스는 함수형 인터페이스가 아닌 타입의 인스턴스를 만들 때 사용

<br>

## 람다식

---

> 이름이 없는 함수 식, 함수형 인터페이스의 인스턴스를 생성

<br>

ex1)
```java
PriorityQueue<Integer> pq = new PriorityQueue<>((o1, o2) -> o2 - o1);

// 메서드 참조를 사용하면 더 간단하게도 표현 가능
PriorityQueue<Integer> pq = new PriorityQueue<>(Collections::reverseOrder());
```
ex2)
```java
import java.util.function.DoubleBinaryOperator;

public enum Operation {
    PLUS((x, y) -> x + y),
    MINUS((x, y) -> x - y),
    TIMES((x, y) -> x * y),
    DIVIDE((x, y) -> x / y);

    private final DoubleBinaryOperator operator;

// 생성자 안의 람다는 인스턴스 필드나 메서드 사용이 불가함에 주의
    Operation(DoubleBinaryOperator operator) {
        this.operator = operator;
    }

    public double apply(double x, double y) {
        return operator.applyAsDouble(x, y);
    }
}
```
같은 코드도 익명 클래스를 사용할 때보다 코드가 짧아진다

<br>

### 주의 사항
- 람다는 한 줄로 표현이 가능한 상황에 사용이 권장
- 타입을 명시해야 코드가 명확해지는 경우, 컴파일러가 타입을 알 수 없다는 오류를 내는 경우를 제외하고는 매개변수의 타입을 생략하고 타입 추론을 활용
  * 타입 추론의 필요한 타입 정보는 대부분 제네릭에서 얻으므로 이를 위해 로 타입이 아닌 제네릭 그리고 제네릭 메서드를 사용
- 람다의 직렬화 형태는 구현별로(가상머신 등) 다르므로 직렬화를 삼가
- 람다는 이름과 문서화가 불가 -> 메서드 참조 활용으로 극복 가능
- 람다의 this는 자신이 아닌 바깥 인스턴스를 가리킴


<br>

 ### 함수형 인터페이스
 > 추상 메서드가 하나인 인터페이스
```java
//java.util.function에서 이외에도 다양한 함수 인터페이스를 제공
@FunctionalInterface
public interface DoubleBinaryOperator {
    double applyAsDouble(double left, double right);
}
```

<br>

