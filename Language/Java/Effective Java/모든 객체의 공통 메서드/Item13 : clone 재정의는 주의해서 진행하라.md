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

<br>

## 재정의 방법

---

### 가변 상태를 참조하지 않는 클래스
```java
    @Override
    public Object clone() {
        try {
            return (PhoneNumber) super.clone(); // 클라이언트가 형변환할 필요 없어짐
        } catch (CloneNotSupportedException e) {
            throw new AssertionError(); // 발생하지 않음, 검사 예외로 구현해서 생긴 문제
        }
    }
```
 - 모든 필드가 기본 타입 혹은 불변 객체를 참조한다면 추가 작업 필요 없음
 - 불변 클래스는 굳이 clone 메서드를 제공할 필요 없음

<br>

### 가변 상태를 참조하는 클래스
#### 1. 단순한 가변 상태
```java
public class Stack {
 private Object[] elements;
 private int size = 0;
}
```
 - 가변 상태를 참조하지 않는 클래스와 같은 방식으로 clone()을 구현하면 elements 필드를 공유하므로 원본과 복제본의 불변식을 보장하지 못함
 - 내부 필드에서도 clone을 재귀적으로 호출해야 함
```java
    @Override
    public Stack clone() {
        try {
            Stack result = (Stack) super.clone();
            result.elements = elements.clone();
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
```
 - 가변 상태를 참조하는 필드가 final이라면 이와 같은 방법은 불가하므로 제거해야 할 수 있음
 - Cloneable 아키텍처는 '가변 객체를 참조하는 필드는 final로 선언하라`와 충돌

<br>

#### 2. 복잡한 가변 상태 
```java
public class HashTable {
 private Entry[] buckets;
}
```
- 단순히 재귀적으로 clone을 호출하면 배열 내부의 Entry가 여전히 공유되는 문제 발생
깊은 복사 사용
```java
    // 재귀 호출로 인해 리스트가 길 때 스택오버플로 발생 위험
    @Override
    Entry deepCopy() {
        return new Entry(key, value, next == null ? null : next.deepCopy());
    }

    @Override
    Entry deepCopy() {
        Entry result = new Entry(key, value, next);
        for (Entry p = result; p.next != null; p = p.next)
            p.next = new Entry(p.next.key, p.next.value, p.next.next);
        return result;
    }

    @Override
    public HashTable clone() {
        try {
            HashTable result = (HashTable) super.clone();
            result.buckets = new Entry[buckets.length];
            for (int i = 0; i < buckets.length; i++)
             if (buckets[i] != null)
              result.buckets[i] = buckets[i].deepCopy();
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
```

- super.clone()을 호출하여 얻은 객체의 모든 필드를 초기 상태로 설정 후 원본 객체의 상태를 다시 생성하는 고수준 메서드를 호출하는 방법은 간단하다는 장점이 있지만 속도가 느리다는 단점이 있음

<br>

## 주의 사항

---

 - 생성자에서는 의도치 않은 객체를 생성할 수 있으므로 재정의될 수 있는 메서드를 호출하지 않아야 함(clone 메서드)
 - 상속용 클래스는 Cloneable을 구현하면 안됨
   * 하위 클래스에서 구현하게 선택
  ```java
  @Override
  protected Object clone() throws CloneNotSupportedException {}
  ```
   * 하위 클래스에서도 재정의 불가
  ```java
  @Override
  protected Object clone() throws CloneNotSupportedException {
   throw new CloneNotSupportedException();
  }
  ```
 - Cloneable을 구현한 스레드 안전 클래스는 clone 메서드도 적절히 동기화 해야 함
 - 일련번호나 고유ID와 같은 경우 기본 타입이나 불변이더라도 수정해야 함

<br>

## 복사 생성자, 복사 팩터리

---

> Cloneable을 구현하지 않은 클래스에서 더 나은 객체 복사 방식
```java
public class Person {
    private String name;
    private int age;

    // 기본 생성자
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 복사 생성자
    public Person(Person other) {
        this.name = other.name;
        this.age = other.age;
    }

    // 복사 팩터리 메서드
    public static Person copy(Person other) {
        return new Person(other.name, other.age);
    }
```

### 장점(clone 메서드의 단점을 극복)
 - 언어 모순적이지 않음
 - 안전한 객체 생성 매커니즘(생성자를 사용)
 - 엉성한 규약에 얽매이지 않음
 - final 필드 용법과 충돌하지 않음
 - 불필요한 검사 예외를 던지지 않음
 - 형변환 불필요
 - 인터페이스 타입의 인수를 받을 수 있음

> 복제 기능은 생성자와 팩터리를 이용하되 배열만은 clone 메서드 사용을 권장
