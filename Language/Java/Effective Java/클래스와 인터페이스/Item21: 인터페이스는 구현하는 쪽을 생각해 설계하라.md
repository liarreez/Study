# 인터페이스는 구현하는 쪽을 생각해 설계하랄

<br>

기존 인터페이스
> 디폴트 메서드로 새 메서드를 추가하는 것은 최대한 피하고 추가하게 될 경우 기존 구현체와의 충돌을 고려

<br>

새로운 인터페이스
> 릴리스 전에 여러 방식으로 구현해보고 클라이언트도 여러 개 만들어 테스트

<br>

## 디폴트 메서드

---

> 인터페이스에 있는 구현된 메서드로 기존 인터페이스에 메서드를 추가할 수 있게 됨

<br>

 - 디폴트 메서드로 추가된 메서드는 기존 구현체의 불변식을 해치거나 오류 발생 가능
    * Collection(기존 인터페이스)에 removeIf(디폴트 메서드)를 추가 한 경우, Collections를 구현한 SynchronizedCollection(구현체)의 동기화와 관련된 부분이 없으므로 여러 스레드가 접근 시 예상치 못한 문제 발생
    * 결국 필요한 구현체의 경우 디폴트 메서드를 재정의하는 등의 작업 필요
 - 디폴트 메서드는 기존 인터페이스로부터 메서드를 제거하거나 기존 메서드의 시그니처를 수정하는 용도로 사용하면 안됨
    * 기존 인터페이스의 메서드가 변경됨으로 구현체가 기존의 메서드 사용 불가
