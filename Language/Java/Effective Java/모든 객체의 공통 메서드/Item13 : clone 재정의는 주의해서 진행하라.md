# clone 재정의는 주의해서 진행하라

<br>

## clone 메서드

---

### 작동 원리
 1. clone 메서드를 사용하려는 클래스가 Cloneable을 구현했는지 확인
 2. Cloneable을 구현했다면 얕은 복사
 3. Cloneable을 구현하지 않았다면 CloneNotSupportedException 예외 발생

<br>

- Cloneable은 clone 메서드 사용이 가능한 클래스인지 알려주는 인터페이스
- clone 메서드는 protected 메서드, 외부에서 사용 시 재정의 필요
- Cloneable을 구현한 클래스는 clone 메서드를 public으로 제공하며, 복제가 제대로 이뤄짐을 기대

<br>

### 일반 규약
 - 객체의 복사본을 생성해 반환
   * x.clone() != x
   * x.clone().getClass == x.getClass()
   * x.clone().equals(x)
   * 위의 식을 반드시 만족할 필요 없음
 - 관례상, 이 메서드가 반환하는 객체는 super.clone을 호출해서 얻음
   * x.clone().getClass() == x.getClass()
   * 하위 클래스의 clone 메서드가 상위 클래스의 타입 객체를 반환하는 문제가 발생할 수 있음
 - 반환된 객체와 원본 객체는 독립적, 특정 필드를 반환전에 수정해야 할 수도 있음
