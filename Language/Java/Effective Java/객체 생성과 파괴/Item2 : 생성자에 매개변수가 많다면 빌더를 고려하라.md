# 생성자에 매개변수가 많다면 빌더를 고려하라

<br>

## 사용 이유

---

 > 정적 팩터리 메서드와 생성자는 모두 선택적 매개변수가 많을 때 대응하기 어려움

<br>

### 점층적 생성자 패턴
> 필수 매개변수는 고정으로 받고 선택 매개변수의 갯수마다 받는 생성자를 만드는 패턴
- 매개변수가 많아지면 각 값의 의미가 불분명해지고 매개변수가 몇 개인지도 주의깊게 세야 함

<br>

### 자바빈즈 패턴
> 매개변수가 없는 생성자 객체를 만들고 setter 메서드를 호출해 매개변수 값을 설정하는 패턴
- 객체를 만들기 위해 메서드를 여러 개 호출해야 하고 완전히 생성되기 전에는 일관성이 무너진 상태
- 일관성 문제 때문에 클래스를 불변으로 만들 수 없고 스레드 안전성을 위해 추가 작업이 필요

<br>

## 빌더 패턴
> 1) 필수 매개변수만으로 생성자를 호출해 빌더 객체를 얻고, 2)일종의 setter 메서드로 선택 매개변수를 설정한 후
> 3) 매개변수가 없는 build 메서드를 호출해 필요한 객체를 얻는 패턴

피자 클래스(추상 클래스)
```java
import java.util.EnumSet;
import java.util.Objects;
import java.util.Set;

public abstract class Pizza {
    public enum Topping {HAM, MUSHROOM, ONION, PEPPER, SAUSAGE}
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);

        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        protected abstract T self();
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }
}
```

뉴욕 피자 클래스(구체 클래스) : 사이즈가 필수 매개변수수
```java
public class NyPizza extends Pizza {
    public enum Size {SMALL, MEDIUM, LARGE}
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override
        public NyPizza build() {
            return new NyPizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }

    @Override
    public String toString() {
        return "NyPizza{" +
                "size=" + size +
                ", toppings=" + toppings +
                '}';
    }
}
```

칼초네 피자 클래스(구체 클래스) : 소스 유무가 필수 매개변수수
```java
public class Calzone extends Pizza {
    private final boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false; // Default

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override
        public Calzone build() {
            return new Calzone(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }

    @Override
    public String toString() {
        return "Calzone{" +
                "sauceInside=" + sauceInside +
                ", toppings=" + toppings +
                '}';
    }
}
```

사용 예제
```java
public class PizzaTest {
    public static void main(String[] args) {
        NyPizza nyPizza = new NyPizza.Builder(NyPizza.Size.MEDIUM)
                .addTopping(Pizza.Topping.SAUSAGE)
                .addTopping(Pizza.Topping.ONION)
                .build();

        Calzone calzone = new Calzone.Builder()
                .addTopping(Pizza.Topping.HAM)
                .sauceInside()
                .build();

        System.out.println(nyPizza);
        System.out.println(calzone);
    }
}
```

### 장점
- 계층적으로 설계된 클래스와 함께 쓰기 좋음, 추상 클래스는 추상 빌더를, 구체 클래스는 구체 빌더를 가짐
- 빌더를 이용하면 가변인수 매개변수를 여러 개 사용할 수 있음(ex) addTopping)
- 빌더의 생성자와 메서드에서 입력 매개변수를 검사하고, build 메서드가 호출하는 생성자에서 여러 매개변수에 걸친 불변식 검사 가능
   * 불변 : 어떠한 변경도 허용하지 않음
   * 불변식 : 프로그램이 실행되거나 정해진 기간 동안 반드시 만족해야 하는 조건, 이 조건 내에서만 변경을 허용

### 단점
 - 코드가 길어짐, 매개변수가 4개 이상일 때 추천
 - 빌더 생성 비용이 크지는 않지만 성능에 민감한 경우 문제가 될 수 있음음
