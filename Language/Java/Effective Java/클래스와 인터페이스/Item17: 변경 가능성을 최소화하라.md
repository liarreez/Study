# 변경 가능성을 최소화하라
> 불변이면 설계, 구현, 사용이 쉬우며 오류도 적게 발생

<br>

## 클래스 불변 규칙

---

 - 객체의 상태를 변경하는 메서드(setter)를 제공하지 않음
 - 클래스를 확장할 수 없음, 상속 불가(final, 정적 팩터리 메서드)
 - 모든 필드를 final로 선언
 - 모든 필드를 private로 선언
   * public final은 가변 객체에서 변경의 가능성이 있고, 불변 객체에서는 내부 표현을 변경 못하는 단점 존재
 - 가변 컴포넌트는 자신만 접근
   * 생성자, 접근자 등 가변 컴포넌트의 반환이 필요하다면 방어적 복사를 수행
 - 객체가 피연산자의 대상인 경우 그 결과 객체를 새로 생성해서 반환(함수형 프로그래밍)
   * 절차형(명령형) 프로그래밍 : 피연산자인 객체를 수정해서 상태를 바꿔 반환
   * 함수형 프로그래밍은 메서드 이름으로 전치사, 절차형 프로그래밍은 메서드 이름을 동사로 짓는 것을 권장

> 완화된 규칙은 어떤 메서스드도 객체의 상태 중 외부에 비치는 값은 변경할 수 없음

<br>

## 불변 클래스 특징

---

 - 불변 객체는 근본적으로 스레드 안전
 - 불변 객체는 안심하고 공유 가능
   * 자주 사용하는 불변 클래스는 재활용 권장
   ```java
   // 자주 사용하는 인스턴스 캐싱
    public static final Boolean TRUE = new Boolean(true);
    public static final Boolean FALSE = new Boolean(false);

   // 정적 팩터리로 캐싱한 인스턴스 공유
       public static Boolean valueOf(boolean b) {
        return b ? Boolean.TRUE : Boolean.FALSE;
    }
   ```
   * 방어적 복사가 불필요, 공유를 활용(String의 복사 생성자 사용 비권장)
  - 불변 객체끼리 내부 데이터 공유 가능
    ```java
    final int signum;
    final int[] mag;

    public BigInteger negate() {
        return new BigInteger(this.mag, -this.signum);
    }
    ```
  - 불변 객체들을 객체의 구성요소로 사용하면 불변식을 유지하기 수월(Map 또는 Set의 키)
  - 실패 원자성(메서드에서 예외가 발생한 후에도 그 객체는 여전히 유효한 상태)를 제공

<br>

### 단점
 - 생성비용이 크더라도 사소한 변화라도 있으면 반드시 독립된 객체로 새로 생성
   * 복잡한 연산을 메서드로 만들어 기본 기능으로 제공(StringBuilder)
   * 클래스를 public으로 제공(String)
