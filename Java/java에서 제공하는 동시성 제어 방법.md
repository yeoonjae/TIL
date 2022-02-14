# **동시성 문제**
싱글쓰레드일 경우엔 하나의 쓰레드만 작업하기 때문에 공유자원에 대해 문제가 발생할 가능성이 낮습니다. 하지만, 멀티쓰레드 환경에서는 여러개의 쓰레드가 하나의 자원을 공유하기 때문에 서로의 작업에 영향을 미칠 수 있습니다. 

코드로 예를 들면 다음과 같은 경우가 있습니다. 
```java
@Slf4j
public class MultiThread {

  private static long count = 0;

  @Test
  void threadNotSafe() throws Exception {
    int max = 100;

    for (int i = 0; i < max; i++) {
      new Thread(() -> {
        count++;
        System.out.println(count);
      }).start();
    }

    Thread.sleep(300); // 모든 스레드가 종료될 때까지 잠깐 대기
    Assertions.assertThat(count).isEqualTo(max);
  }
}
```
* 하나의 자원인 count 가 있습니다. 
* 100 개의 쓰레드를 만들어 count 를 읽고, 이를 증가시킵니다. 
* 100개의 쓰레드이니, count 값은 분명 100이 되어야합니다. 

하지만, 이를 실행시켰을 때 다음과 같은 결과가 나오는 것을 볼 수 있습니다. 

![image](https://user-images.githubusercontent.com/63777714/153756176-4af699b8-c6c3-4773-a603-464841ca002c.png)

숫자가 뒤엉키고, 결국 100이 아닌 다른 값을 찍어냅니다. 

때문에 이런 일이 발생하지 않도록 멀티 쓰레드 환경에서 하나의 쓰레드가 작업이 끝나기 전까지 다른 쓰레드가 제어하지 못하도록 하는 것이 필요합니다. 이를 위해 도입된 개념이 **임계영역**과 **잠금(lock)** 입니다. 

> 🤷‍♂️ **임계영역**<br>
> 프로세스간에 공유자원에 접근하는데에 있어 문제가 발생하지 않도록 한번에 하나의 프로세스만 이용하게끔 보장해줘야하는 영역입니다. <br>
> 🤷‍♂️ **잠금(lock)** <br>
> 하나의 쓰레드나 프로세스가 자원을 사용하고 있는 동안 잠금을 하여 접근하지 못하게 하는 방법입니다. 

공유자원을 사용하는 동안 코드영역을 `임계영역`으로 지정하고 공유자원을 가지고있는 `잠금(lock)`을 획득한 쓰레드만 `임계영역`내의 코드를 수행할 수 있고, 이를 모두 수행하면 잠금을 반납합니다. 그리고 다른 쓰레드가 잠금을 획득하여 코드를 수행하는 방법입니다. 

그럼 Java 에서 이처럼 동시성 제어를 위해 제공해주는 방법에 대해서 알아보도록 하겠습니다. 

# **Java 에서 제공하는 동시성 제어 방법**
## **1. synchronized**
 `synchronized` 키워드를 사용하여 임계영역을 설정할 수 있습니다. `synchronized` 가 선언된 블럭에는 동시에 하나의 쓰레드만 접근할 수 있습니다. 

`synchronized`는 메소드 전체, 특정 블럭에만 적용할 수 있습니다. 

```java

  public synchronized void synchronizedTest() {
    // 메소드 전체에 적용
  }
  public void synchronizedUnitTest() {
    synchronized (MultiThread.class) {
      // 특정 블럭에만 적용
    }
  }
```
처음에 작성했던 코드에 `synchronized`를 적용시키면 다음과 같이 작성할 수 있습니다. 
```java
  @Test
  void threadNotSafe() throws Exception {
    int max = 100;

    for (int i = 0; i < max; i++) {
      new Thread(this::run).start();
    }

    Thread.sleep(300); // 모든 스레드가 종료될 때까지 잠깐 대기
    Assertions.assertThat(count).isEqualTo(max);
  }

  public synchronized void plus() {
    count++;
  }
```
이렇게 메소드 또는 특정블럭에 `synchronized`를 선언해주어 임계영역을 지정하고 lock을 획득한 쓰레드가 작업이 끝나기 전까지 다른 쓰레드가 접근하지 못하도록 하면 동기화를 할 수 있습니다.

하지만 이는 다음 쓰레드가 현재 작업을 수행하고 있는 쓰레드를 기다리는 현상을 야기합니다. 따라서 이 방법을 사용한다면 메소드 전체에 `synchronized`를 선언하는 것보다 동기화가 필요한 특정블럭에만 `synchronized`를 선언하여 임계영역을 최소화하는 것이 좋습니다. 

## **2. volatile - 자원의 가시성**
여러개의 쓰레드가 공유자원에 동시에 접근할 때마다 메모리에 접근하지 않습니다. 성능을 위해 메모리에서 읽어온 값을 **CPU 캐시**에 저장하고 **CPU 캐시**에서 값을 읽어서 작업합니다. 다시 같은 값을 읽어올 때는 먼저 CPU 캐시에 있는지 확인하고 없을때에만 메모리에서 읽어옵니다. 그렇기 때문에 멀티쓰레드 환경에서 메모리에 있는 공유자원을 사용하려해도 캐시때문에 원하는 값이 나오지 않을 가능성이 있습니다. 

![image](https://user-images.githubusercontent.com/63777714/153758136-8af3d812-d7d3-4ce3-95b0-f2a24742c82a.png)

자바에서의 `volatile` 키워드는 위의 문제점을 해결해줄 수 있는 멀티쓰레드 환경에서 동기화를 해주는 키워드입니다. 변수 앞에 `volatile`을 붙이면 변수의 값을 읽어올 때 캐시값을 읽어오는 것이 아니라 메모리에 있는 값을 읽어오기 때문에 캐시와 메모리 간의 값 불일치하는 문제를 해결할 수 있습니다. 

실제 메모리에 저장된 값을 조회함으로써 자원의 가시성을 확보할 수 있습니다. 

![image](https://user-images.githubusercontent.com/63777714/153758165-e6ed19a5-b16b-46c6-bd7a-859e3933260d.png)

하지만, 가시성을 확보한 것만으로 동시성 이슈를 해결하기엔 충분하지 않습니다. 읽기를 메모리에서 읽어오지만 쓰기 작업에서 동시성 이슈가 발생할 수 있기 떄문입니다. `volatile`이 효과적인 경우는 하나의 쓰레드가 쓰기작업을 하는 동안 다른 쓰레드가 읽기 작업만 하는 경우입니다. 즉, **원자성을 보장하는 경우에만 동기화를 보장**합니다. 

문제가 되었던 코드엔 다음과 같이 적용할 수 있습니다. 
```java
@Slf4j
public class MultiThread {

  private static volatile long count = 0;

  @Test
  void threadNotSafe() throws Exception {
    int max = 10000;

    for (int i = 0; i < max; i++) {
      new Thread(() -> count++).start();
    }

    Thread.sleep(300); // 모든 스레드가 종료될 때까지 잠깐 대기
    Assertions.assertThat(count).isEqualTo(max);
  }
}
```
위의 코드는 쓰기 작업을 동시에 하기때문에 원자성을 보장하지 않습니다. 따라서 다음과 같은 결과를 초래할 수 있습니다. 

![image](https://user-images.githubusercontent.com/63777714/153758117-2b4b04f7-10ff-404d-9ddc-8fd8eeb3452a.png)


## **3. Atomic 클래스**
* `java.util.concurrent.atomic`
* Java 에서는 멀티쓰레드 환경에서 thread-safe한 개발을 할 수 있도록 `Atomic` 클래스를 제공합니다. 
* `Atomic` 클래스는 CAS(compare-and-swap)을 이용하여 동시성을 보장합니다. 그리고 AtomicInteger는 synchronized 보다 적은 비용으로 동시성을 보장합니다. 

> CAS 란 변수의 값을 변경하기 전에 내가 예상한 값인지 확인하여 같은 경우에만 값을 할당하는 알고리즘입니다. Thread-safe 를 위해 자바에서 제공하는 Automic Type 클래스가 CAS 알고리즘을 기반으로 동기화 문제를 해결합니다. 
> ```java
> public class AtomicExample {
>    int val;
>    
>    public boolean compareAndSwap(int oldVal, int newVal) {
>       if(val == oldVal) {
>            val = newVal;
>            return true;
>        } else {
>            return false;
>        }
>    }
>}
> ```
> 코드로 보자면 다음과 같습니다. 변수를 선언하고 내가 예상한 값이 맞다면 새 값을 할당하고, 아닐경우 false 를 반환합니다. 즉, 값을 변경하기 전에 한 번 더 확인합니다. 

Automic 클래스에서 대표적인 클래스인 AtomicInteger를 살펴보겠습니다. 
```java
public class AtomicInteger extends Number implements java.io.Serializable {
 
    private volatile int value; // volatile 키워드 적용되어 가시성 보장
    ...
    public final int incrementAndGet() {
        return U.getAndAddInt(this, VALUE, 1) + 1;
    }
}

public final class Unsafe {
    ...
    // 메모리에 저장된 값과 CPU에 캐시된 값을 비교해 동일한 경우에만 update 수행
    public final int getAndAddInt(Object o, long offset, int delta) {
        int v;
        do {
            v = getIntVolatile(o, offset);
        } while (!weakCompareAndSetInt(o, offset, v, v + delta));
        return v;
    }
}
```

* 전역변수 value를 `volatile`이 적용되어 있습니다.
* 연산 작업에서는 값이 맞는지 확인한 후 값을 할당하는 CAS 가 적용되어 있습니다.

----
참고
 - https://devwithpug.github.io/java/java-thread-safe/
 - https://velog.io/@been/%EC%9E%90%EB%B0%94Multi-Thread%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-%EB%8F%99%EC%8B%9C%EC%84%B1-%EC%A0%9C%EC%96%B4%EB%A5%BC-%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95