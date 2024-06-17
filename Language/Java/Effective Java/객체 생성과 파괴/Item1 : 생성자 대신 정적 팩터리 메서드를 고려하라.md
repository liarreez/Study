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

 <br>

 ### 4. 입력 매개변수에 따라 다른 클래스의 객체를 반환할 수 있음
 ```java
 public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
     Enum<?>[] universe = getUniverse(elementType);
     if (universe == null)
         throw new ClassCastException(elementType + " not an enum");

     if (universe.length <= 64)
         return new RegularEnumSet<>(elementType, universe);
     else
         return new JumboEnumSet<>(elementType, universe);
 }
 ```
EnumSet은 원소가 없으면 예외를, 64개 이하면 long으로 관리하는 RegularEnumSet을, 65개 이상이면 long배열로 관리하는 JumboEnumSet 반환

<br>

### 5. 정적 팩터리 메서드를 작성하는 시점에 반환할 객체의 클래스가 없어도 됨
 #### 서비스 제공자 프레임워크
> 클라이언트 프로그램이 특정 서비스의 구현체를 프레임워크가 통제하여 동적으로 검색하고 로드할 수 있게 해주는 패턴

##### 구성요소
- 서비스 인터페이스 : 서비스의 기능 정의, `Connection`
- 서비스 제공자 등록 API : 서비스의 구현체를 등록, `com.mysql.cj.jdbc.DriverManager.registerDriver`
- 서비스 접근 API : 클라이언트가 서비스의 인스턴스를 얻음 `java.sql.DriverManager.getConnection`
- 서비스 제공자 인터페이스 : 서비스 인터페이스의 인스턴스를 생성 `java.sql.Driver`

```java
 Driver mySqlDriver = new com.mysql.cj.jdbc.Driver();
 DriverManager.registerDriver(mySqlDriver);

 Connection connection = DriverManager.getConnection(url, user, password);
```

<br>

## 정적 팩터리 메서드 단점

---

### 1. 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없음

<br>

- 상속을 하려면 public이나 protected 생성자가 필요
- 상속보다 컴포지션을 사용하는 관점에서 장점이 되기도 함

<br>

### 2. 정적 팩터리 메서드는 찾기 어려움
 - API 문서를 잘 쓰고 메서드 이름도 규약을 따라 지음으로 문제를 완화
 #### 정정 팩터리 명명 방식
 - from : 하나의 매개변수로 해당 타입의 인스턴스를 반환하는 형변환 메서드
 - of : 여러 매개변수로 적절한 인스턴스를 반환하는 집계 메서드
 - valueOf : from과 of보다 더 다양한 형태의 매개변수를 받아 인스턴스 반환
 - instance / getInstance : 매개변수로 명시한 인스턴스를 반환, 같은 인스턴스임을 보장하지 않음
 - create / newInstance : instance, getInstance와 동일하지만 매번 새로운 인스턴스를 반환함을 보장
 - getType : getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 사용, Type은 반환할 객체의 타입
 - newType : newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 사용, Type은 반환할 객체의 타입
 - type : getType과 newType의 간결한 형태

