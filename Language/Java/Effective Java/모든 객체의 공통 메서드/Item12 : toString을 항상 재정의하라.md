# toString을 항상 재정의하라

> 간결하면서 유익한 정보를 반환해야 객체를 출력할 때(println, 디버거 등) 유용함

## 유의점
 ```java
public class Person {
    private String name;
    private int age;
    private double height;

    public Person(String name, int age, double height) {
        this.name = name;
        this.age = age;
        this.height = height;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public double getHeight() {
        return height;
    }

    @Override
    public String toString() {
        // 포맷을 명시한 경우
        return String.format("Name: %s, Age: %d, Height: %.2f cm", name, age, height);
    }

    @Override
    public String toString() {
        // 포맷을 명시하지 않았지만 의도를 밝힌 경우
        return "Person{name='" + name + "', age=" + age + ", height=" + height + "}";
    }
```
 - toString은 객체가 가진 주요 정보 모두를 반환,객체가 거대하거나 문자열이면 요약 정보를 반환
  * 3개의 필드가 있는데 2개만 반환하는 경우 Assertion failure: expected {20, 175}, but was {20, 175}과 같은 문제 발생
 - 포맷을 명시하면 객체는 표준적이고 명확하며 읽기 용이함
  * 포맷을 명시하면 명시한 포맷에 맞는 문자열을 반환
  * 객체를 상호 전환할 수 있는 정적 팩터리나 생성자 제공 권장
  * 포맷을 명시하면 그 포맷에 얽매임
  * 포맷을 명시하지 않더라도 의도는 명확히 밝혀야 함
 - toString이 반환한 값에 정보를 얻어올 수 있는 API를 제공(getter)
  * 접근자를 제공하지 않으면 toString의 반환값을 파싱해야하므로 비효율적

## toString 재정의가 불필요한 경우
 - 정적 유틸리티 클래스(Math) : 사용 안함
 - 열거 타입(Enum) : 이미 제공
 - 공유할 문자열이 없는 추상 클래스 : 하위 클래스에서 재정의, 공유할 문자열이 있다면 상위 클래스에서 재정의
 - 자동 생성(AutoValue) : 의미를 더하기 위해 재정의 가능
