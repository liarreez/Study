# 톱레벨 클래스는 한 파일에 하나만 담으라

> 컴파일 순서에 따라 한 클래스가 다르게 정의되는 문제 발생

<br>

```java
public class Main {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
}

// Utensil.java
class Utensil {
    static final String NAME = "pan";
}

class Dessert {
    static final String NAME = "cake";
}

// Dessert.java
class Utensil {
    static final String NAME = "pot";
}

class Dessert {
    static final String NAME = "pie";
}
```

## 결과
 - javac Main.java Dessert.java : 컴파일 오류
 - javac Main.java 또는 javac Main.java Utensil.java : pancake
 - javac Dessert.java Main.java : potpie

<br>

## 해결방안
 - 톱레벨 클래스들을 서로 다른 소스 파일로 분리
 - 정적 멤버 클래스를 사용

