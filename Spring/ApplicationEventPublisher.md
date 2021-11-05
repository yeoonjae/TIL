> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **ApplicationEventPublisher**
이벤트 프로그래밍이 필요한 인터페이스를 제공합니다. 옵저버 패턴 구현체입니다.

> 🔸 **옵저버 패턴**이란?<br>
> 옵저버 패턴은 객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을때마다 메서드 중 하나를 호출하여 자동으로 옵저버들에게 통보하는 소프트웨어 디자인 패턴입니다. <br><br>
즉, <u>**어떤 객체의 상태가 변할 때 그와 연관된 객체들에게 알림을 보내는 디자인 패턴**</u>입니다. 

---

## **ApplicationContext extends ApplicationEventPublisher**

![image](https://user-images.githubusercontent.com/63777714/140501377-62ab99e5-bc0a-43ba-ba92-24b27c05147d.png)

위의 그림에서 보는 것과 같이 `ApplicationContext` 은 `ApplicationEventPublisher` 인터페이스를 구현하고 있습니다. `publishEvent(ApplicationEvent event)` 메소드를 통해 이벤트를 발생시킬 수 있습니다. 

그렇다면 이벤트를 만들고 이를 `ApplicationEventPublisher` 를 통해 발생시키고, 확인까지 해보는 코드를 작성해보겠습니다. 

--- 
 ##  **1. 이벤트 만들기**

 ```java
 package me.whiteship.dempspring51;

import org.springframework.context.ApplicationEvent;

// 이 이벤트를 발생시킬 수 있는 기능을 ApplicationContext 가 가지고 있다. (ApplicationEventPublisher)
public class MyEvent extends ApplicationEvent {

    private int data;

    // 기본 생성자
    public MyEvent(Object source) {
        super(source);
    }

    // 원하는 데이터를 담아서 보낼 수 있다
    public MyEvent(Object source, int data) {
        super(source);
        this.data = data;
    }

    public int getData() {
        return data;
    }
}
 ```
 * 이벤트 코드입니다. 
 * `ApplicationEvent`를 상속받습니다. 
 * Spring 4.2 부터는 `ApplicationEvent`를 상속받지 않더라도 이벤트로 사용할 수 있습니다. 

---
##  **2. 이벤트 발생**

```java
@Component
public class AppRunner implements ApplicationRunner {
// ApplicationContext 역시 ApplicationEventPublisher를 구현하여 사용도 가능 
//    @Autowired
//    ApplicationContext applicationContext;

    @Autowired
    ApplicationEventPublisher publishEvent;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        publishEvent.publishEvent(new MyEvent(this, 100));

    }
}
```

* 이벤트를 발생시키는 코드입니다. 
* `ApplicationEventPublisher.publishEvent()` 메소드를 사용하여 이벤트를 발생시킬 수 있습니다. 

---
##  **3. 이벤트 처리하는 방법**

```java
@Component // 빈으로 등록
public class MyEventHandler implements ApplicationListener<MyEvent> {

    @Override
    public void onApplicationEvent(MyEvent event) {
        System.out.println("이벤트를 받았다. 데이터는 " + event.getData());
    }
}
```
* 이벤트를 처리하는 코드입니다. 
* `ApplicationListener<이벤트>` 구현한 클래스 만들어서 빈으로 등록합니다. 
* Spring 4.2 부터는 `@EventListener` 를 사용하여 빈의 메소드에서 사용할 수 있습니다. 
* 기본적으로는 `synchronuzied` 입니다. 
* 순서를 정하고 싶다면 `@Order`와 함께 사용합니다. 
* 비동기적으로 실행하고 싶다면 `@Async`와 함께 사용합니다. 

```java
// Spring 4.2부터는 ApplicationListener<MyEvent> 구현을 생략할 수 있다.
@Component
public class MyEventHandler{

    @EventListener
    @Order(Ordered.HIGHEST_PRECEDENCE) // 순서를 지정하는 어노테이션 (나중에 찍고싶다면 Ordered.HIGHEST_PRECEDENCE +2)를 해주면 된다. 
    @Async // 비동기적으로 실행하고 싶을 때 사용
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트를 받았다. 데이터는 " + event.getData());
    }
}

```

---
## **스프링이 제공하는 기본 이벤트**
* ContextRefreshedEvent: ApplicationContext를 초기화 했더나 리프래시 했을 때 발생합니다. 
* ContextStartedEvent: ApplicationContext를 start()하여 라이프사이클 빈들이 시작
신호를 받은 시점에 발생합니다.
* ContextStoppedEvent: ApplicationContext를 stop()하여 라이프사이클 빈들이 정지
신호를 받은 시점에 발생합니다.
* ContextClosedEvent: ApplicationContext를 close()하여 싱글톤 빈 소멸되는 시점에
발생합니다.
*  RequestHandledEvent: HTTP 요청을 처리했을 때 발생합니다. 

적용 코드
```java
import org.springframework.context.event.ContextClosedEvent;
import org.springframework.context.event.ContextRefreshedEvent;
import org.springframework.context.event.EventListener;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Component;

@Component
public class AnotherHandler {

    @EventListener
    @Async
    public void handle(MyEvent myEvent) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("Another : " + myEvent.getData());
    }

    @EventListener
    public void handle(ContextRefreshedEvent event) {   // ApplicationContext 를 초기화하거나 리프레시 했을 때 발생
        System.out.println(Thread.currentThread().toString());
        System.out.println("ContextRefreshedEvent");
    }

    @EventListener
    public void handle(ContextClosedEvent event) {  // ApplicationContext를 Close()하여 싱글톤 빈 소멸되는 시점에 발생
        System.out.println(Thread.currentThread().toString());
        System.out.println("ContextClosedEvent");
    }

}
```