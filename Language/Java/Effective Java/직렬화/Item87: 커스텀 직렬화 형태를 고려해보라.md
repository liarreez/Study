# 커스텀 직렬화 형태를 고려하라

> 기본 직렬화의 단점이 명확하다면 커스텀 직렬화를 통해 해결하

<br>

## 커스텀 직렬화 사용 상황

---

- 객체의 물리적 표현과 논리적 표현의 차이가 클 때
- 불변식 보장과 보안을 위한 역직렬화(readObject)가 필요할 때

private 필드(@Serial) 혹은 메서드(@SerialData)더라도 공개 API에 속할 경우 문서화 주석을 작성해야 함
<br>

### 기본 직렬화 사용시 문제점
- 공개 API가 현재 내부 표현 방식에 종속되어 이후에 내부 표현 방식을 바꿔도 관련 코드가 유지
- 불필요한 데이터를 포함하여 직렬화 하므로 시간과 공간이 낭비됨
- 스택 오버플로 발생 가능
- 불변식이 세부 구현에 따라 달라지는 경우 정확성을 해침

```java
// 기본 직렬화를 사용해도 되는 예시

// name이 null이 아님을 보장(불변식 보장), age가 민감한 정보(보안)일 경우
// readObject를 제공해야 함
class Person implements Serializable {
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
}
```

<br>

```java

// 커스텀 직렬화가 유리한 예시
class CustomDate implements Serializable {
    private static final long serialVersionUID = 1L;

    // 물리적 표현: 여러 필드로 저장된 복잡한 형태의 날짜 정보
    private transient int day;
    private transient int month;
    private transient int year;

    // 논리적 표현: long 타입의 타임스탬프
    public CustomDate(int day, int month, int year) {
        this.day = day;
        this.month = month;
        this.year = year;
    }

    // 커스텀 직렬화: 논리적 표현인 long 타입의 타임스탬프로 직렬화
    private void writeObject(ObjectOutputStream out) throws IOException {
        out.defaultWriteObject(); // 기본 직렬화 수행
        LocalDate date = LocalDate.of(year, month, day);
        long timestamp = date.atStartOfDay(ZoneId.systemDefault()).toEpochSecond(); // 논리적 표현인 타임스탬프로 변환
        out.writeLong(timestamp); // 논리적 표현으로 직렬화
    }

    // 커스텀 역직렬화: 논리적 표현인 long 타입의 타임스탬프에서 복원
    private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
        in.defaultReadObject(); // 기본 역직렬화 수행
        long timestamp = in.readLong(); // 논리적 표현인 타임스탬프를 읽음
        LocalDate date = LocalDate.ofEpochDay(timestamp / (24 * 60 * 60)); // 다시 물리적 표현인 day, month, year로 복원
        this.day = date.getDayOfMonth();
        this.month = date.getMonthValue();
        this.year = date.getYear();
    }
}

```

<br>

## 커스텀 직렬화 방법

---

- 기본 직렬화 형태에 포함되지 않을 인스턴스 필드에 transient 한정자 작성
  * 기본 직렬화 메서드를 호출하면 transient로 선언하지 않은 모든 인스턴스 필드가 직렬화
  * 논리적 상태와 무관한 필드임이 확실할 때만 transient 생략 가능
  * 기본 직렬화 사용 시 transient 필드들은 기본값(0, null, false 등)으로 초기화
- 인스턴스 필드 변화에도 호홛되기 위해 직렬화(writeObject), 역직렬화(readObject)는 기본 직렬화 형태를 호출

<br>

## 기본 직렬화 사용 여부와 상관없이 공통 적용

---

- 객체의 전체 상태를 읽는 메서드에 적용해야 하는 동기화 메커니즘(락 순서 등)
- 직렬 버전 UID를 명시적으로 부여
  * 아무 long 값을 선택, 중복도 가능
  * 구버전과의 호환성을 유지하려면 직렬 버전 UID를 그대로 사용, 끊으려면 수정


