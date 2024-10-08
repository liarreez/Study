# 공유 중인 가변 데이터는 동기화해 사용하라

<br>

> 여러 스레드에서 사용되는 데이터는 불변 데이터 사용을 우선 고려하고 가변 데이터를 사용한다면 동기화를 해주자

<br>

## 가변 데이터

---

 - 가변 데이터는 단일 스레드에서만 사용
 - 데이터를 공유해야 한다면 불변 데이터 사용을 우선 고려
 - 유지보수과정에서도 지켜지기 위해 문서 작성
 - 사용하는 프레임워크와 라이브러리의 정책 파악

<br>

## 가변 데이터를 공유할 때

---

> 공유중인 가변 데이터는 동기화하자

<br>

### 동기화

<br>

#### 1. 가시성(visibility)

> 한 스레드가 만든 변화를 다른 스레드에서 확인할 수 있어야 함

<br>

```java
class FlagExample {
    private boolean stopRequested = false;

    public void requestStop() {
        stopRequested = true;
    }

    public void runTask() {
        while (!stopRequested) {
            // 작업을 계속 수행한다.
        }
        System.out.println("Task stopped.");
    }
}
```

- 하나의 스레드는 runTask()를 수행하고 다른 스레드가 requestStop()을 수행한다고 가정
- runTask()를 수행하는 스레드는 stopRequested의 변화를 읽지 못하고 계속 수행
- 해결 방법은 private volatile boolean stopRequested을 사용하면 가시성을 얻을 수 있음

<br>

#### 2. 원자성(atomicity)
> 한 스레드가 데이터를 변경 중이라면 다른 스레드가 중간의 과정을 보지 못하게 해 작업이 완전히 수행되게 함

<br>

```java
class Counter {
    private volatile int count = 0;

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}
```

- ++연산은 읽고 더하고 쓰는 세단계를 거침
- 한 스레드가 increment()를 호출하고 count값을 읽고 더하기 전에(count = 0)
- 다른 스레드가 increment()를 호출하면 count값을 읽음(count = 0)
- 결과적으로 두 스레드 모두 0에 1을 더해서 결과는 1이되지만 의도한 답은 increment()를 두번 호출했으므로 2여야 함
- 안전 실패 : 프로그램이 잘못된 결과를 계산해내는 오류

<br>

### 동기화를 하는 방법

<br>

#### 1. synchronized
> 메서드나 블록을 한 번에 하나의 스레드만 접근할 수 있게 하여 원자성과 메모리 가시성을 보장하는 자바 키워드

<br>

```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

- 한 스레드가 increment()를 실행하는 동안 다른 스레드는 접근할 수 없음
- 한 스레드가 ++연산의 읽고 더하고 쓰는 작업이 끝난 후에야 다른 스레드가 변경된 값을 읽고 작업 수행
- volatile가 필요 없음

<br>

#### 2. Atomic을 이용한 락-프리 동기화
> 락 없이 스레드간 경쟁을 CAS알고리즘을 사용해서 처리함으로 원자적인 연산을 제공해 스레드 간 안전한 데이터 처리를 가능하게 하는 기법

- CAS 알고리즘 : 변수의 값을 비교한 후 예상하는 값이면 새로운 값으로 교체하는 원자적 연산을 수행
- ABA 문제 : AS가 예상한 값(A)와 현재 값(A)을 비교해 값이 동일하다고 생각하지만, 실제로는 그 값이 중간에 다른 값(B)로 변경되었다가 다시 원래 값(A)로 돌아왔기 때문에 발생하는 문제
-  * AtomicStampedReference : 값과 스탬프를 같이 비교하여 ABS 문제를 해결
   * AtomicMarkableReference : 값이 변경되었는지 여부를 boolean 마커로 표시하여 ABS 문제를 해결
<br>

```java
import java.util.concurrent.atomic.AtomicInteger;

class Counter {
    private AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.getAndIncrement();
    }

    public int getCount() {
        return count.get();
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        // 두 개의 스레드가 동시에 count를 증가시킴
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                counter.increment();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                counter.increment();
            }
        });

        t1.start();
        t2.start();
        
        t1.join();
        t2.join();
        
        System.out.println("Final count: " + counter.getCount()); // 결과: 20
    }
}

```

- 스레드 1이 count.getAndIncrement() 호출
  * count를 읽고 예상값을 0으로 설정
  * 예상값과 count 비교 후 동일하면 count 1증가(count = 1)
- 스레드 1과 2가 동시에 count.getAndIncrement() 호출
  * count를 읽고 스레드 1과 스레드 2의 예상값은 1로 설정
  * 스레드 1이 예상값과 count 비교 후 동일하므로 count 1증가(count = 2)
  * 스레드 2는 예상값과 count가 다르므로 값을 다시 읽고 CAS 시도 후 예상값(2)와 count가 같으므로 count 1 증가(count = 3)

<br>

#### synchronized와 atomic 사용

<br>

- 단순하고 고성능을 요구하는 상황에 atomic이 적합
- 복잡한 동기화나 여러 리소스를 관리해야 하는 경우, 또는 경합이 매우 높은 환경에서 syncronized가 적합합


