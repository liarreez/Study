# equals를 재정의하려거든 hashCode도 재정의하라

<br>

> equals를 재정의하고 hashCode를 재정의하지 않으면 hashCode 일반 규약을 어기게 되고 hashCode를 사용할 때 문제 발생

<br>

## hashCode 일반 규약

---

 - equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 hashCode 메서드는 항상 같은 값을 반환해야 함
 - equals가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 함
 - equals가 두 객체를 다르다고 판단했더라도 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없음
   * 다른 객체에 대해서 다른 값을 반환해야 해시테이블의 성능이 좋아짐
   * 해시는 일반적으로 입력의 범위보다 작은 범위의 고정된 길이 값을 출력하므로 어떤 서로 다른 객체는 서로 같은 해시값을 가지게 됨

<br>

전화번호 객체
```java
class Person {
    private int id;
    private String name;

    public Person(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return id == person.id && Objects.equals(name, person.name);
    }

    // hashCode 메서드를 재정의하지 않음
}
```
equals에서 두 객체를 동일하게 판단했찌만 두 객체의 hashCode가 다른 값을 반환
```java
    public static void main(String[] args) {
        Set<Person> personSet = new HashSet<>();
        Person person1 = new Person(1, "John");
        Person person2 = new Person(1, "John");

        personSet.add(person1);
        personSet.add(person2);

        System.out.println("Set size: " + personSet.size()); // 기대 출력: 1, 실제 출력: 2
    }
```

<br>

## 해시 테이블 작동 방식

---

 1. 해시 코드 계산: 객체의 해시 코드를 계산하여 해시 테이블의 특정 버킷 결정
 2. 버킷 선택: 해시 코드에 따라 해당 객체를 저장할 버킷을 선택
 3. 버킷 내 검색: 버킷 내에서 객체를 검색하거나 삽입할 때 equals 메서드를 사용하여 정확한 동치성을 확인

> 두 인스턴스가 같은 버킷에 있더라도 해시코드가 다를 경우 동치성 비교를 하지 않음

<br>

## 간단한 좋은 hashCode 작성 요령

---

### 직접 작성
 1. 초기값 설정
  - 첫번째 핵심 필드의 해시 코드를 계산한 값이 초기값
 2. 핵심 필드 해시 코드 계산
  - 핵심 필드 : equals 비교에 사용되는 필드
    * 기본 타입이라면 Type.hashCode(f) 수행
    * 참조 타입이라면 이 필드(복잡해진다면 이 필드의 표준형)의 hashCode를 호출, null이면 관습적으로 0 사용
    * 배열이라면 핵심 원소 각각을 별도 필드처럼 다루고 결합, 핵심 원소가 하나도 없으면 관습적으로 0을 사용, 모든 원소가 핵심 원소라면 Arrays.hashCode를 사용
  - 다른 필드로부터 계산하는 파생 필드는 계산에서 제외 가능
  - 핵심 필드가 아니라면 반드시 계산에서 제
 3. 결합 : 2에서 구한 해시코드로 result 갱신
    - 곱셈을 사용해야 필드를 곱하는 순서에 따라 result 값이 크게 달라짐
      * 31 * a + b != 31 * b + a
      * a + b + c = b + c + a = c + a + b
    - 31을 사용하는 이유
      * 오버플로 발생 시 짝수를 곱하면 최하위 비트가 0, 홀수를 곱하면 최하위 비트가 1이므로 홀수가 정보 손실이 적음
      * 소수는 해시 코드의 충돌을 줄여주고 균등하게 분포될 수 있도록 함

```java
@Override
public int hashCode() {
    int result = Integer.hashCode(id); // 초기 값
     result = 31 * result + (name != null ? name.hashCode() : 0); // 필드 해시 코드 계산 + 결합
    return result;
}
```

<br>

### Objects.hash()
```java
@Override
public int hashCode() {
    return Objects.hash(lineNum, prefix, areaCode);
}
```
코드는 간결해지지만 속도는 더 느리다

<br>

### 캐싱
> 클래스가 불변이고 해시 코드 계산 비용이 크다면 인스턴스가 생성될 때 계산해두고 사용

- 해시 코드가 처음 요청될 때 해시 코드를 계산하는 방법도 있지만 스레드 안정성을 고려해야 함(해시 코드가 달라질 수 있음)

<br>

## 주의 사항

---

 - 해시 코드가 좁은 범위에 집중될 수 있으므로 해시 코드 계산 시 핵심 필드를 생략하면 안됨
 - 해시 코드의 유지 보수를 위해 해시 코드가 반환하는 값의 생성 규칙을 API 사용자에게 알려주면 안됨
