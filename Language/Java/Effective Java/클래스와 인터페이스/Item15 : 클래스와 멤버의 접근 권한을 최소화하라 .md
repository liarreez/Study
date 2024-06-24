# 클래스와 멤버의 접근 권한을 최소화 하라

<br>

## 캡슐화(정보 은닉)

---

> 내부 구현을 숨겨 API와 분리

### 장점
 - 컴포넌트를 병렬로 개발할 수 있어 개발 속도 높임
 - 컴포넌트별로 디버깅, 교체가 용이해 시스템 관리 비용을 낮춤
 - 특정 컴포넌트만 최적화할 수 있어 성능 최적화에 도움
 - 재사용성 증대
 - 개별 컴포넌트를 개발하고 테스트할 수 있어 큰 시스템 제작 난이도를 낮춤

<br>

## 접근 제한자

---

### 종류
 - public : 모든 곳에서 접근 가능, 공개 API
 - protected : 동일 패키지 혹은 하위 클래에서 접근 가능, 멤버에만 사용 가능, 공개 API
 - package-private : 멤버가 소속된 패키지 안의 모든 클래스에 접근 가능, 비공개 API
   * 한 클래스에서만 사용하는 클래스는 private static으로 중첩 클래스를 사용)
 - private : 멤버를 선언한 톱레벨 클래스에서 접근 가능, 멤버에만 사용 가능, 비공개 API

### 유의 사항
 - 모든 클래스와 멤버의 접근성을 가능한 좁힌다(private)
 - 멤버의 접근성을 넓히는 일이 많다면 컴포넌트 분해를 고민한다
 - Serializable을 구현한 클래스는 직렬화, 역직렬화 과정에서 private, package-private 필드에 접근 가능
 - 공개 API는 지속적으로 지원 되어야 하고 동작 방식을 문서에 작성해야 할 수 있음
 - 상위 클래스의 매서드를 재정의할 때 접근 수준은 상위 클래스보다 좁게 설정할 수 없음(인터페이스)
 - 테스트만을 위해 클래스, 인터페이스, 멤버를 공개 API로 만들 필요는 없음
 - public 클래스의 인스턴스 필드가 public이면 불변식을 보장할 수 없고 스레드 안전하지 않음
 - 클래스가 표현하는 추상 개념을 완성하는 데 꼭 필요한 구성요소써의 상수라면 public static final로 공개 가능
   * 기본 타입 값이나 불변 객체를 참조해야 함
   * 가변 객체는 참조된 객체를 수정하는 방법으로 수정 될 수 있음
     ```java
        //가변 객체 사용 방법
     
        private static final Thing[] PRIIVATE_VALUES = {...};
     
        // public 불변 리스트 사용
        public static final List<Thing> VALUES =
                Collections.unmodifiableList((Arrays.asList(PRIIVATE_VALUES)));
     
        // 복사본을 반환하는 public 메서드 추
        public static final Thing[] values() {
            return PRIIVATE_VALUES.clone();
        }
     ```
