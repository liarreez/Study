# 다 쓴 객체 참조를 해제하라

<br>

> 자바에서 가비지 컬렉터가 메모리를 자동으로 관리해주지만 메모리 관리에 전혀 신경 쓰지 않아도 되는 것은 아님

<br>

## 메모리 누수 원인
 ### 1. 다 쓴 객체의 참조
```java
public class IntegerList {
    private int[] numbers;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 10;
    
    IntegerList() {
        numbers = new int[DEFAULT_INITIAL_CAPACITY];
    }
    
    public void push(int number) {
        // 원소 공간 확보 로직 생략
        numbers[size++] = number;
    }
    
    public int removeLast() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return numbers[--size];
        // 사용하지 않는 숫자들도 여전히 참조를 하고 있어 메모리 누수 발생 가능
    }  
}
```

<br>

---
### 해결 방안

 #### 참조를 담은 변수를 유효 범위 밖으로 밀어 냄

유효 범위를 너무 넓게 잡아서 반복 후에도 iterateSize 변수가 메모리에 남음
 ```java
public class Main {
    private static int iterateSize = 3;
    public static void main(String[] args) {
        List<Integer> list = List.of(1, 2, 3, 4, 5, 6, 7);
        for (int index = 0; index < iterateSize; index++) {
            Syste거
        listener = null;

        System.out.println("Listener registered and eligible for GC.");
    }
}
```


