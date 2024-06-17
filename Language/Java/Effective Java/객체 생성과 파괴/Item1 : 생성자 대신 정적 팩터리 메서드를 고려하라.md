# 생성자 대신 정적 팩터리 메서드를 고려하라

<br>

## 정적 팩터리 메서드
> 클래스의 인스턴스를 반환하는 정적 메서드
```java
    static <E> List<E> of(E e1, E e2, E e3) {
        return new ImmutableCollections.ListN<>(e1, e2, e3);
    }
```

<br>

## 정적 팩터리 메서드 장점

---

### 1. 이름 소유

<br>

 #### 1) 객체의 특성 묘사
 ```java
 public static User createManager(String username) {
   return new User(username, true);
 }

 public static User createCustomer(String username) {
   return new User(username, false);
 }
 ```
true, false로 불분명한 객체의 특성을 이름에 Manager 혹은 Customer를 사용함으로 명확히 할 수 있음

<br>

 #### 2) 한 클래스에 시그니처가 같은 여러 팩터리 메서드 사용 가능
 ```java
 public class User {
    private final String username;
    private final String id;
    private final String email;

    public User(String username, String id, String email) {
        this.username = username;
        this.id = id;
        this.email = email;
    }


    // 생성자 사용, 컴파일 에러 발생(오버로딩 제약)

    public User(String username, String id) {
        this(username, id, "" );
    }

    public User(String username, String email) {
        this(username, "", email);
    }

    // 정적 팩터리 메서드 사용

    public static User createWithId(String username, String id) {
        return new User(username, id, "" );
    }

    public static User createWithEmail(String username, String email) {
        return new User(username, "", email);
    }
}
 ```

<br>

### 2. 호출될 때마다 인스턴스를 새로 생성하지 않음
```java
public class Runtime {
    private static final Runtime currentRuntime = new Runtime();

    private Runtime() {}

    public static Runtime getRuntime() {
        return currentRuntime;
    }
}
```
현재 실행과 관련된 인스턴스는 여러 개가 필요없음, 정적 메서드를 활용하면 이런 불필요한 객체 생성을 피할 수 있음

<br>

 #### 1) 클래스를 싱글턴으로 만들 수 있음
 #### 2) 클래스를 인스턴스화 불가하게 만들 수 있음
 #### 3) 동치인 인스턴스가 단 하나임을 보장(a == b 일 때만 a.equals(b)가 성립)

 <br>

 ### 3. 반환 타입의 하위 타입 객체를 반환할 수 있음
 구현 클래스를 공개하지 않고 그 객체를 반환함으로 API를 작게 유지할 수 있음
 ```java
 public static <T> List<T> unmodifiableList(List<? extends T> list) {
   return (list instanceof RandomAccess ?
     new UnmodifiableRandomAccessList<>(list) :
     new UnmodifiableList<>(list));
 } 
 ```
 인스턴스화 불가 클래스 Collections에서는 위와 같이 여러 인터페이스에 특정 기능(수정 불가, 동기화 등)이 더해진 구현체를 정적 팩터리 메서드를 통해 제공
 
