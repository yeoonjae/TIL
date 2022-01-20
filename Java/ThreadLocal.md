# **동시성 문제와 ThreadLocal**

## **동시성**
동시성 문제란 <u>**여러 쓰레드가 동시에 같은 인스턴스의 필드 값을 변경하면서 발생하는 문제**</u>를 말합니다. 이런 동시성 문제는 여러 쓰레드가 같은 인스턴스의 필드에 접근해야 하기 때문에 트래픽이 적은 상황에서는 확률상 잘 나타나지 않고, 트래픽이 많을수록 자주 발생합니다. 특히 스프링 빈 처럼 싱글톤 객체의 필드를 변경하며 사용할 때 이러한 동시성 문제를 조심해야 합니다. 

동시성 문제가 발생하는 코드를 보며 이해해보도록 하겠습니다. 
```java
@Slf4j
public class FieldService {

  private String nameStore;

  public String logic(String name) {
    log.info("저장 name={} -> nameStore={}", name, nameStore);
    nameStore = name;
    sleep(1000);
    log.info("조회 nameStore={}", nameStore);
    return nameStore;
  }

  private void sleep(int millis) {
    try {
      Thread.sleep(millis);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
  }
}
```
```java
@Slf4j
public class FieldServiceTest {

  private FieldService fieldService = new FieldService();

  @Test
  void field() {
    log.info("main start");

    Runnable userA = () -> {
      fieldService.logic("userA");
    };

    Runnable userB = () -> {
      fieldService.logic("userB");
    };

    Thread threadA = new Thread(userA);
    threadA.setName("thread-A");
    Thread threadB = new Thread(userB);
    threadB.setName("thread-B");
    threadA.start(); //A실행

//    sleep(2000); //동시성 문제 발생X
    sleep(100); //동시성 문제 발생O
    threadB.start(); //B실행

    sleep(3000); //메인 쓰레드 종료 대기
    log.info("main exit");

  }

  private void sleep(int millis) {
    try {
      Thread.sleep(millis);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
  }
}

```

위 코드는 fieldService 클래스에 `name` 이라는 인스턴스 변수를 선언해주고 `login()` 메소드에서 이를 저장하고 조회하도록 되어있습니다. 그리고 ThreadA와 ThreadB 를 동작시켜 동시성 문제가 발생하도록 했습니다. 

위 코드를 실행시키면 다음과 같은 결과가 나옵니다. 

![image](https://user-images.githubusercontent.com/63777714/150276872-eaacc0ac-623a-4e31-b90c-e964beac65a9.png)


정상적인 동작순서 즉, 우리가 원하는 동작순서는 다음과 같습니다. 
1. `ThreadA` 는 `userA`를 저장한다. 
2. `ThreadA` 는 `userA`를 조회한다. 
2. `ThreadB` 는 `userB`를 저장한다. 
2. `ThreadB` 는 `userB`를 조회한다. 

하지만, 위 코드의 동작순서는 다음과 같습니다. 
1. `ThreadA` 는 `userA`를 저장한다. 
2. `ThreadB` 는 `userB`를 저장한다. 
2. `ThreadA` 는 `userB`를 조회한다. 
2. `ThreadB` 는 `userB`를 조회한다. 

![image](https://user-images.githubusercontent.com/63777714/150276966-7089da36-b4b0-4228-9198-07fdd5325008.png)

![image](https://user-images.githubusercontent.com/63777714/150276994-49ea3f33-691f-4232-a1b2-262afc284af4.png)

![image](https://user-images.githubusercontent.com/63777714/150277024-9bf4c0bd-d893-4139-a194-2eb9495381b3.png)

이러한 문제를 어떻게 해결하는 것이 좋을까요? 바로 쓰레드 로컬을 사용해서 해결할 수 있습니다. 

>💡 동시성 문제는 지역변수에선 발생하지 않습니다. 지역변수는 각 쓰레드마다 다른 메모리 영역을 할당하기 때문입니다. 
동시성 문제가 발생하는 곳은 인스턴스의 필드(주로 싱글톤) 또는 static 같이 공용 필드에 접근할 경우에 발생합니다. 또한, 동시성 문제는 값을 읽기만 할때는 발생하지 않고 어디선가 값을 쓸 때 발생하게 됩니다. 

---
## **ThreadLocal 이란**
**ThreadLocal 은 해당 쓰레드만 접근할 수 있는 특별한 저장소**를 말합니다. 여러사람이 같은 물건 보관 창고를 사용하더라도 창구 직원은 사용자를 인식해서 사용자 별로 물건을 구분해줍니다. A,B 사용자 모두 창구 직원을 통해 물건을 보관하고 꺼내지만 창구 직원은 사용자에 따라 보관된 물건을 구분해주는 것입니다. 

ThredLocal을 사용하면 각 쓰레드마다 별도의 내부 저장소를 제공합니다. 따라서 같은 인스턴스의 쓰레드 로컬에 접근해도 문제가 되지 않습니다. 

![image](https://user-images.githubusercontent.com/63777714/150280403-8d6473ee-1128-4da8-a33c-628ec9d6da9c.png)

위의 그림처럼 ThreadLocal을 통하면 쓰레드 별 전용 저장소에 데이터를 저장하고 전용 저장소에서 이를 조회하고 반환합니다. 따라서 동시에 접근해도 다른 저장소에 저장하기 때문에 동시성 이슈를 피할 수 있습니다. 

`java.lang.ThreadLocal` 클래스를 지원합니다.
<br><br>

### **ThreadLocal 시용법**
* 값 저장 : `ThreadLocal.set('xxx')`
* 값 조회 : `ThreadLocal.get()`
* 값 제거 : `ThreadLocal.remove()`
<br><br>

### **ThreadLocal 사용 예제**
```java
@Slf4j
public class ThreadLocalService {

  private ThreadLocal<String> nameStore = new ThreadLocal<>();

  public String logic(String name) {
    log.info("저장 name={} -> nameStore={}", name, nameStore.get());
    nameStore.set(name);
    sleep(1000);
    log.info("조회 nameStore={}", nameStore.get());
    return nameStore.get();
  }

  private void sleep(int millis) {
    try {
      Thread.sleep(millis);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
  }
}
```
```java
@Slf4j
public class ThreadLocalServiceTest {

  private ThreadLocalService threadLocalService = new ThreadLocalService();

  @Test
  void field() {
    log.info("main start");

    Runnable userA = () -> {
      threadLocalService.logic("userA");
    };

    Runnable userB = () -> {
      threadLocalService.logic("userB");
    };

    Thread threadA = new Thread(userA);
    threadA.setName("thread-A");
    Thread threadB = new Thread(userB);
    threadB.setName("thread-B");
    threadA.start(); //A실행

//    sleep(2000); //동시성 문제 발생X
    sleep(100); //동시성 문제 발생O
    threadB.start(); //B실행

    sleep(3000); //메인 쓰레드 종료 대기
    log.info("main exit");

  }

  private void sleep(int millis) {
    try {
      Thread.sleep(millis);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
  }
}
```

실행결과는 다음과 같이 나옵니다. 

![image](https://user-images.githubusercontent.com/63777714/150281435-6584c957-c812-41a3-99b8-1aa15088f7dd.png)

---
## **ThreadLocal 주의사항**
Tomcat처럼 쓰레드풀을 사용하는 WAS가 있습니다. 쓰레드를 미리 생성해놓고 이를 재사용하는 방법이죠. 요청이 들어올 때마다 이를 만들어 사용한다면 많은 비용이 발생하기 때문에 많은 WAS에서 쓰레드 풀을 이용해 미리 만들어 놓고 사용합니다. 

때문에 이럴 경우 ThreadLocal을 이용해서 값을 저장한 후 `remove()`를 하지 않으면 심각한 문제가 발생할 수 있습니다. 그림을 보며 이해해보도록 하겠습니다. 

![image](https://user-images.githubusercontent.com/63777714/150287526-cf7ea416-4f78-4828-9405-a6aade95d09a.png)

1. 사용자가 저장하는 HTTP 요청을 보내면 Thread Pool을 사용하는 WAS는 Thread Pool 에 있는 Thread 중 하나(`Thread-A`)를 선택해서 일을 수행할 것 입니다. 
2. ThreadLocal을 사용할 경우 ThreadLocal에 의해 해당 `Thread-A` 의 값을 저장하는 저장소에 저장됩니다. 
3. 이 상태로 `remove()`를 하지 않고 HTTP 응답이 끝날 경우 사용된 `Thread-A`는 다시 Thread Pool에 반납됩니다. 
    * remove()를 하지 않았으니 `Thread-A`의 저장소에 저장되어 있는 값은 그대로 유지가 됩니다. 
4. 이 상태에서 다른 사용자가 HTTP 요청을 보내 Thread-A 를 사용하게 되고, 만약 ThreadLocal에 있는 값을 조회한다면?
5. 사용자가 저장했던 값을 다른 사용자가 볼 수 있게 됩니다. 

그렇기 때문에 ThreadLocal을 사용할 땐 반드시 요청이 끝날 떄 `ThreadLocal.remove()`를 해주어야 함을 기억합시다. 

---
참고
- 스프링 핵심 원리-고급편 (인프런_김영한님)