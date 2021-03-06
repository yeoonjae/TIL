> π λ³Έ κΈμ μΈνλ° κ°μ 'μ€νλ§ ν΅μ¬ κΈ°μ 'μ λ£κ³  μ λ¦¬ν κΈμλλ€. 

<br>

# **ApplicationEventPublisher**
μ΄λ²€νΈ νλ‘κ·Έλλ°μ΄ νμν μΈν°νμ΄μ€λ₯Ό μ κ³΅ν©λλ€. μ΅μ λ² ν¨ν΄ κ΅¬νμ²΄μλλ€.

> πΈ **μ΅μ λ² ν¨ν΄**μ΄λ?<br>
> μ΅μ λ² ν¨ν΄μ κ°μ²΄μ μν λ³νλ₯Ό κ΄μ°°νλ κ΄μ°°μλ€, μ¦ μ΅μ λ²λ€μ λͺ©λ‘μ κ°μ²΄μ λ±λ‘νμ¬ μν λ³νκ° μμλλ§λ€ λ©μλ μ€ νλλ₯Ό νΈμΆνμ¬ μλμΌλ‘ μ΅μ λ²λ€μκ² ν΅λ³΄νλ μννΈμ¨μ΄ λμμΈ ν¨ν΄μλλ€. <br><br>
μ¦, <u>**μ΄λ€ κ°μ²΄μ μνκ° λ³ν  λ κ·Έμ μ°κ΄λ κ°μ²΄λ€μκ² μλ¦Όμ λ³΄λ΄λ λμμΈ ν¨ν΄**</u>μλλ€. 

---

## **ApplicationContext extends ApplicationEventPublisher**

![image](https://user-images.githubusercontent.com/63777714/140501377-62ab99e5-bc0a-43ba-ba92-24b27c05147d.png)

μμ κ·Έλ¦Όμμ λ³΄λ κ²κ³Ό κ°μ΄ `ApplicationContext` μ `ApplicationEventPublisher` μΈν°νμ΄μ€λ₯Ό κ΅¬ννκ³  μμ΅λλ€. `publishEvent(ApplicationEvent event)` λ©μλλ₯Ό ν΅ν΄ μ΄λ²€νΈλ₯Ό λ°μμν¬ μ μμ΅λλ€. 

κ·Έλ λ€λ©΄ μ΄λ²€νΈλ₯Ό λ§λ€κ³  μ΄λ₯Ό `ApplicationEventPublisher` λ₯Ό ν΅ν΄ λ°μμν€κ³ , νμΈκΉμ§ ν΄λ³΄λ μ½λλ₯Ό μμ±ν΄λ³΄κ² μ΅λλ€. 

--- 
 ##  **1. μ΄λ²€νΈ λ§λ€κΈ°**

 ```java
 package me.whiteship.dempspring51;

import org.springframework.context.ApplicationEvent;

// μ΄ μ΄λ²€νΈλ₯Ό λ°μμν¬ μ μλ κΈ°λ₯μ ApplicationContext κ° κ°μ§κ³  μλ€. (ApplicationEventPublisher)
public class MyEvent extends ApplicationEvent {

    private int data;

    // κΈ°λ³Έ μμ±μ
    public MyEvent(Object source) {
        super(source);
    }

    // μνλ λ°μ΄ν°λ₯Ό λ΄μμ λ³΄λΌ μ μλ€
    public MyEvent(Object source, int data) {
        super(source);
        this.data = data;
    }

    public int getData() {
        return data;
    }
}
 ```
 * μ΄λ²€νΈ μ½λμλλ€. 
 * `ApplicationEvent`λ₯Ό μμλ°μ΅λλ€. 
 * Spring 4.2 λΆν°λ `ApplicationEvent`λ₯Ό μμλ°μ§ μλλΌλ μ΄λ²€νΈλ‘ μ¬μ©ν  μ μμ΅λλ€. 

---
##  **2. μ΄λ²€νΈ λ°μ**

```java
@Component
public class AppRunner implements ApplicationRunner {
// ApplicationContext μ­μ ApplicationEventPublisherλ₯Ό κ΅¬ννμ¬ μ¬μ©λ κ°λ₯ 
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

* μ΄λ²€νΈλ₯Ό λ°μμν€λ μ½λμλλ€. 
* `ApplicationEventPublisher.publishEvent()` λ©μλλ₯Ό μ¬μ©νμ¬ μ΄λ²€νΈλ₯Ό λ°μμν¬ μ μμ΅λλ€. 

---
##  **3. μ΄λ²€νΈ μ²λ¦¬νλ λ°©λ²**

```java
@Component // λΉμΌλ‘ λ±λ‘
public class MyEventHandler implements ApplicationListener<MyEvent> {

    @Override
    public void onApplicationEvent(MyEvent event) {
        System.out.println("μ΄λ²€νΈλ₯Ό λ°μλ€. λ°μ΄ν°λ " + event.getData());
    }
}
```
* μ΄λ²€νΈλ₯Ό μ²λ¦¬νλ μ½λμλλ€. 
* `ApplicationListener<μ΄λ²€νΈ>` κ΅¬νν ν΄λμ€ λ§λ€μ΄μ λΉμΌλ‘ λ±λ‘ν©λλ€. 
* Spring 4.2 λΆν°λ `@EventListener` λ₯Ό μ¬μ©νμ¬ λΉμ λ©μλμμ μ¬μ©ν  μ μμ΅λλ€. 
* κΈ°λ³Έμ μΌλ‘λ `synchronuzied` μλλ€. 
* μμλ₯Ό μ νκ³  μΆλ€λ©΄ `@Order`μ ν¨κ» μ¬μ©ν©λλ€. 
* λΉλκΈ°μ μΌλ‘ μ€ννκ³  μΆλ€λ©΄ `@Async`μ ν¨κ» μ¬μ©ν©λλ€. 

```java
// Spring 4.2λΆν°λ ApplicationListener<MyEvent> κ΅¬νμ μλ΅ν  μ μλ€.
@Component
public class MyEventHandler{

    @EventListener
    @Order(Ordered.HIGHEST_PRECEDENCE) // μμλ₯Ό μ§μ νλ μ΄λΈνμ΄μ (λμ€μ μ°κ³ μΆλ€λ©΄ Ordered.HIGHEST_PRECEDENCE +2)λ₯Ό ν΄μ£Όλ©΄ λλ€. 
    @Async // λΉλκΈ°μ μΌλ‘ μ€ννκ³  μΆμ λ μ¬μ©
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("μ΄λ²€νΈλ₯Ό λ°μλ€. λ°μ΄ν°λ " + event.getData());
    }
}

```

---
## **μ€νλ§μ΄ μ κ³΅νλ κΈ°λ³Έ μ΄λ²€νΈ**
* ContextRefreshedEvent: ApplicationContextλ₯Ό μ΄κΈ°ν νλλ λ¦¬νλμ νμ λ λ°μν©λλ€. 
* ContextStartedEvent: ApplicationContextλ₯Ό start()νμ¬ λΌμ΄νμ¬μ΄ν΄ λΉλ€μ΄ μμ
μ νΈλ₯Ό λ°μ μμ μ λ°μν©λλ€.
* ContextStoppedEvent: ApplicationContextλ₯Ό stop()νμ¬ λΌμ΄νμ¬μ΄ν΄ λΉλ€μ΄ μ μ§
μ νΈλ₯Ό λ°μ μμ μ λ°μν©λλ€.
* ContextClosedEvent: ApplicationContextλ₯Ό close()νμ¬ μ±κΈν€ λΉ μλ©Έλλ μμ μ
λ°μν©λλ€.
*  RequestHandledEvent: HTTP μμ²­μ μ²λ¦¬νμ λ λ°μν©λλ€. 

μ μ© μ½λ
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
    public void handle(ContextRefreshedEvent event) {   // ApplicationContext λ₯Ό μ΄κΈ°ννκ±°λ λ¦¬νλ μ νμ λ λ°μ
        System.out.println(Thread.currentThread().toString());
        System.out.println("ContextRefreshedEvent");
    }

    @EventListener
    public void handle(ContextClosedEvent event) {  // ApplicationContextλ₯Ό Close()νμ¬ μ±κΈν€ λΉ μλ©Έλλ μμ μ λ°μ
        System.out.println(Thread.currentThread().toString());
        System.out.println("ContextClosedEvent");
    }

}
```

---
## μ λ¦¬
  * `ApplicationContext`λ `ApplicationEventPublisher`λ₯Ό κ΅¬ννκ³  μμ΄ μ΄λ²€νΈλ₯Ό λ°μμν¬ μ μμ΅λλ€. 
  * μ΄λ²€νΈλ₯Ό λ°μμν€λ λ©μλλ `publishEvent(μ΄λ²€νΈλͺ)` μλλ€. 
  * Spring 4.2 μ΄νλΆν°λ μ΄λ²€νΈ μμ± λ° μ΄λ²€νΈ νΈλ€λ¬ ν΄λμ€λ₯Ό λ§λ€ λ `ApplicationEvent` μμκ³Ό `ApplicationListener<μ΄λ²€νΈλͺ>` κ΅¬νμ νμ§ μμλ λ©λλ€. 
  * Spring 4.2 λΆν° μ΄λ²€νΈ λ¦¬μ€λλ `@EventListener`μ μ¬μ©νμ¬ λΉμ λ©μλλ‘ μ¬μ©ν  μ μμ΅λλ€. 
  * κΈ°λ³Έμ μΌλ‘ Springμ ApplicationContext κ° μ€νλ  λ, μ΄κΈ°ν λ  λ, μ’λ£λ  λ λ± λ€μν μ΄λ²€νΈλ₯Ό μ κ³΅ν΄μ€λλ€. 
  * `ApplicationEventPublisher`λ₯Ό μ¬μ©ν  λ μ΄λ₯Ό κ΅¬ννκ³  μλ `ApplicationContext`λ₯Ό μ£Όμλ°μ μ¬μ©νλ κ² λ³΄λ€ κ΅¬μ²΄μ μΈ μΈν°νμ΄μ€λ₯Ό μ£Όμλ°μ μ¬μ©νλ κ²μ΄ μ§κ΄μ μ΄λ―λ‘ κ΅¬μ²΄μ μΈ μΈν°νμ΄μ€λ₯Ό μ§μ  μ£Όμλ°λ κ²μ μ§ν₯ν©λλ€. 



























