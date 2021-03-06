> π λ³Έ κΈμ μΈνλ° κ°μ 'μ€νλ§ ν΅μ¬ κΈ°μ 'μ λ£κ³  μ λ¦¬ν κΈμλλ€. 

<br>

# **μ€νλ§ AOP (Aspect Oriented  Programming)**

**AOP**λ **Aspect Oriented  Programming**μ μ½μλ‘ κ΄μ  μ§ν₯ νλ‘κ·Έλλ°μλλ€. ν©μ΄μ§ κ΄μ¬μ¬(Aspect)λ₯Ό λͺ¨λν ν  μ μλ νλ‘κ·Έλλ° κΈ°λ²μ΄λΌκ³  μκ°νλ©΄ λ©λλ€. 

**μ½λλ ν΅μ¬κ΄μ¬μ¬μ ν‘λ¨ κ΄μ¬μ¬**λ‘ μ΄λ£¨μ΄μ Έ μμΌλ©° ν΅μ¬κ΄μ¬μ¬λ λͺ¨λλ³λ‘ λ€λ₯΄μ§λ§ ν‘λ¨ κ΄μ¬μ¬λ λͺ¨λλ³λ‘ λ°λ³΅λμ΄ μ€λ³΅ν΄μ λνλλ λΆλΆμλλ€. 

AOPμμ κ° κ΄μ μ κΈ°μ€μΌλ‘ λ‘μ§μ λͺ¨λννλ€λ κ²μ μ½λλ€μ λΆλΆμ μΌλ‘ λλμ΄μ λͺ¨λννκ² λ€λ κ² μλλ€. μ΄λ, μμ€μ½λμμμ λ€λ₯Έ λΆλΆμ κ³μ λ°λ³΅ν΄μ μ°μ΄λ μ½λλ€μ λ°κ²¬ν  μ μλλ° μ΄κ²μ **ν©μ΄μ§ κ΄μ¬μ¬(Crosscutting Concerns)** λΌκ³  ν©λλ€. 

![image](https://user-images.githubusercontent.com/63777714/141775047-29af6433-887c-490a-a091-1a55e8437b7e.png)

μμ μ¬μ§λ€κ³Ό κ°μ΄ ν©μ΄μ§ κ΄μ¬μ¬λ₯Ό Aspectλ‘ λͺ¨λννκ³  **ν΅μ¬μ μΈ λΉμ¦λμ€ λ‘μ§μμ λΆλ¦¬νμ¬ μ¬μ¬μ©νκ² λ€λ κ²μ΄ AOPμ μ·¨μ§**μλλ€. 

---
## **AOP μ£Όμ κ°λ**
* **Aspect** : ν©μ΄μ§ κ΄μ¬μ¬λ₯Ό λͺ¨λν ν κ². μ¦, κ°κ°μ λͺ¨λμ μλ―Έν¨
* **Target** : μ μ©μ΄ λλ λμ
* **Advice** : ν΄μΌν  μΌ λ€
* **Join Point** : Adviceκ° μ μ©λ  μμΉ, λΌμ΄λ€ μ μλ μ§μ . μλ₯Όλ€λ©΄, λ©μλ μ€ν μμ ,μμ±μ νΈμΆνκΈ° μ§μ , νλμ μ κ·ΌνκΈ° μ§μ ... λ± λ€μν μμ μ μ μ© κ°λ₯
* **Point Cut** : **Join Pointμ κ΅¬μ²΄νν κ²**μΌλ‘ 'Aν΄λμ€μ Bλ©μλ νΈμΆν  λ μ μ©ν΄λΌ'μ²λΌ κ΅¬μ²΄μ μΌλ‘ Adviceκ° μ€νλ  μ§μ μ μ ν¨ ( μ΄λμ μ μ©ν΄μΌ νλμ§ )

---
## **AOP κ΅¬νμ²΄**
 * JAVA
    * AspectJ
    * μ€νλ§ AOP
---
## **AOP μ μ© λ°©λ²**
* **μ»΄νμΌ** (.java -> .class) : μ»΄νμΌμ΄ λλ μμ μ μ μ©νλ λ°©λ²
* **λ‘λ© νμ** : μ»΄νμΌμ κ±°μΉ ν΄λμ€νμΌμ λ‘λ©νλ μμ μ μ μ©νλ λ°©λ²
* **λ°νμ** : Beanμ λ§λ€ λ(AλΌλ ν΄λμ€λ₯Ό λΉμΌλ‘ λ±λ‘νλ μμμ λ°νμμμ μΌμ΄λ¨) μ μ©νλ λ°©λ² 

---
## **μ€νλ§ AOP νΉμ§**
* **νλ‘μ κΈ°λ°μ AOP κ΅¬νμ²΄**, μ κ·Όμ μ΄ λ° λΆκ°κΈ°λ₯μ μΆκ°νκΈ° μν΄ νλ‘μ κ°μ²΄λ₯Ό μ¬μ©ν¨
* **μ€νλ§ Beanμλ§ AOPλ₯Ό μ μ©**ν  μ μμ
* λͺ¨λ  AOP κΈ°λ₯μ μ κ³΅νλ κ²μ΄ λͺ©μ μ΄ μλλΌ, μ€νλ§ IoCμ μ°λνμ¬ μν°νλΌμ΄μ¦ μ νλ¦¬μΌμ΄μμμ κ°μ₯ νν λ¬Έμ μ λν ν΄κ²°μ±μ μ κ³΅νλ κ²μ΄ λͺ©μ , μ¬κΈ°μ νν λ¬Έμ λΌλ κ²μ μ€λ³΅μ½λ, νλ‘μ ν΄λμ€ μμ±μ λ²κ±°λ‘μ λ±μ λ§ν©λλ€. 

<details markdown="1">
<summary>νλ‘μ ν¨ν΄</summary>

### **νλ‘μ ν¨ν΄**

![image](https://user-images.githubusercontent.com/63777714/141782732-d95f202b-bd45-449e-9aef-a98a696e08ae.png)

 * μ AOPλ νλ‘μ ν¨ν΄μ μ¬μ©νλκ°?
    * κΈ°μ‘΄ μ½λ λ³κ²½ μμ΄ μ κ·Ό μ μ΄ λλ λΆκ°κΈ°λ₯ μΆκ° κ°λ₯


νλ‘μ ν¨ν΄μ μ¬μ©νμ¬ κΈ°μ‘΄μ μ½λλ₯Ό κ±΄λλ¦¬μ§ μκ³  μ±λ₯μΈ‘μ μ ν΄λ³΄λλ‘ νκ² μ΅λλ€. 
```java
public interface EventService {

    void createEvent();

    void publishEvent();

    void deleteEvent();
}

```
```java

@Service
public class SimpleEventService implements EventService {

    @Override
    public void createEvent() {
        long begin = System.currentTimeMillis();

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Create an Event");

        System.out.println(System.currentTimeMillis() - begin);

    }


    @Override
    public void publishEvent() {
        long begin = System.currentTimeMillis();

        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("publish an Event");

        System.out.println(System.currentTimeMillis() - begin);
    }

    public void deleteEvent() {
        System.out.println("Delete an Event");
    }
}
```
```java
@Component
public class AppRunnerEvent implements ApplicationRunner {

    @Autowired
    EventService eventService;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        eventService.createEvent();
        eventService.publishEvent();
        eventService.deleteEvent();
    }
}

```
```java
Create an Event
1010
publish an Event
2014
Delete an Event
```

νλ‘μ ν¨ν΄ μ μ©
```java
@Service
public class SimpleEventService implements EventService {

    @Override
    public void createEvent() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Create an Event");
    }


    @Override
    public void publishEvent() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("publish an Event");
    }

    public void deleteEvent() {
        System.out.println("Delete an Event");
    }
}

```
```java
@Primary // κ°μ νμμ λΉμ΄ μ¬λ¬κ°μ§ μΌ λ κ·Έ μ€ νλλ₯Ό μ ννλ μ΄λΈνμ΄μ
@Service
public class ProxySimpleEventService implements EventService {

    // Proxyμ κ²½μ° Subjectμ λΉμ μ£Όμλ°μμ μ¬μ©ν΄μΌν©λλ€.
    // EventServiceλ‘ λ±λ‘μ ν΄λ λμ§λ§ λ³μλͺμ Subject λͺμΌλ‘ μ¬μ©ν΄μΌ ν©λλ€.
    @Autowired
    SimpleEventService simpleEventService;

    @Override
    public void createEvent() {
        long begin = System.currentTimeMillis();
        simpleEventService.createEvent();
        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void publishEvent() {
        long begin = System.currentTimeMillis();
        simpleEventService.publishEvent();
        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void deleteEvent() {
        simpleEventService.deleteEvent();
    }
}

```
μ€νκ²°κ³Ό
```java
Create an Event
1016
publish an Event
2013
Delete an Event

```

νλ‘μ ν΄λμ€ νμΌμ λ§λ€μ΄μ κΈ°μ‘΄ μμ€μ½λμ λ³κ²½μμ΄ μ±λ₯μ νμ€νΈ νμμ΅λλ€. νμ§λ§, μ΄ λ°©λ²μλ λ€μκ³Ό κ°μ λ¬Έμ μ μ΄ μμ΅λλ€. 
* λ§€λ² νλ‘μ ν΄λμ€λ₯Ό μμ±ν΄μΌ νλκ°?
* μ¬λ¬ ν΄λμ€ μ¬λ¬ λ©μλμ μ μ©νλ €λ©΄?
* κ°μ²΄λ€ κ΄κ³λ λ³΅μ‘

μμ λ¬Έμ λ₯Ό ν΄κ²°νκΈ° μν΄ λ±μ₯ν κ²μ΄ **μ€νλ§ AOP**μλλ€. 


</details>

---

## **μ€νλ§ AOP : @AOP**

μ΄λΈνμ΄μ κΈ°λ°μ @AOP 

μ€νλ§ @AOP μ μ¬μ©νλ €λ©΄ μμ‘΄μ±μ λ€μκ³Ό κ°μ΄ μΆκ°ν΄μΌ ν©λλ€. 
```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

`@Aspect` μ΄λΈνμ΄μμ λΆμ¬ Aspectλ₯Ό λνλ΄λ ν΄λμ€ μμ λͺμνκ³ , `@Component`λ₯Ό λΆμ¬ λΉμΌλ‘ λ±λ‘ν©λλ€. 
```java
@Component
@Aspect
public class PerfAspect {

    @Around("execution(* com.example..*.EventService.*(..))") // λ©μλλ₯Ό κ°μΈλ ννλ‘ μ μ©μ΄ λ¨
    public Object loObject(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}

```
`@Around` μ΄λΈνμ΄μμ λ©μλλ₯Ό κ°μΈλ ννλ‘ μ μ©μ΄ λλ©°, Adviceλ₯Ό μ€ννκ² λ€λ μλ―Έκ° λ©λλ€. `@Around()`κ΄νΈ μμλ PointCutμ μ΄λ¦μ μ€ μλ μκ³ , pointCutμ μ§μ  μ μν  μλ μμ΅λλ€. 

μ¦, `loObject()` λΌλ `Advice`λ₯Ό μ΄λ»κ² μ μ©ν  κ²μΈκ°? λ°λ‘ `@Around`λ‘ μ μ©μ νκ² λ€λ κ² μλλ€. `@Around`λ λ©μλ νΈμΆ μ , ν , μλ¬λ°μμ μ μ©λ ν  μ μμ΅λλ€. 

`execution(* com.example..*.EventService.*(..))`λ com.example νμμ ν¨ν€μ§ κ²½λ‘μ `EventService` κ°μ²΄μ λͺ¨λ  λ©μλμ μ΄ Aspectλ₯Ό μ μ©νκ² λ€λ λ»μλλ€. 

μ μ©μν¬ μ½λμλλ€. 
```java
public interface EventService {

    void createEvent();

    void publishEvent();

    void deleteEvent();
}

```
```java
@Service
public class SimpleEventService implements EventService {

    @Override
    public void createEvent() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Create an Event");
    }

    @Override
    public void publishEvent() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("publish an Event");
    }

    public void deleteEvent() {
        System.out.println("Delete an Event");
    }
}

```
μ€νκ²°κ³Ό
```java
Create an Event
1021
publish an Event
2010
Delete an Event
0
```
createEvent(), publishEvent(), deleteEvent() λͺ¨λ μ±λ₯μΈ‘μ μ΄ μ μ©λ κ²μ νμΈν  μ μμ΅λλ€. 

νμ§λ§ EventService ν΄λμ€ λ΄μ deleteEvent()λ μ±λ₯μΈ‘μ μΌλ‘ νκ³ μΆμ§ μμ κ²½μ°μ μ΄λ»κ² ν΄μΌν κΉμ?

λ°λ‘ **μ΄λΈνμ΄μμ λ§λ€μ΄μ μ΄λΈνμ΄μ κΈ°λ°μΌλ‘ PointCutμ μ§μ νμ¬ μ¬μ©**νλ λ°©λ²μ΄ μμ΅λλ€. 
```java
@Retention(RetentionPolicy.CLASS) // μ΄ μ΄λΈνμ΄μ μ λ³΄λ₯Ό μ΄λμ λ κΉμ§ μ μ§ν  κ²μΈκ° κΈ°λ³Έκ°μ CLASS
@Target(ElementType.METHOD) // λ©μλκ° νκ²μ
@Documented
public @interface PerLogging {

}
```
```java
@Component
@Aspect
public class PerfAspect {

    //μμ λ°©λ²μ μ μ©μ μμΉμλ deleteEventλ μ μ©μ΄ λμ΄λ²λ¦Ό μ΄λ°κ²½μ°μ μ΄λΈνμ΄μμΌλ‘ νμ©νλ κ²μ΄ μ’μ
    @Around("@annotation(PerLogging)")
    public Object loObject(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}

```
μ±λ₯μΈ‘μ μ νκ³ μΆμ λ©μλμλ§ λ§λ€μ΄λμ μ΄λΈνμ΄μμ μ μ©μν€λ©΄ λ©λλ€. 
```java
@Service
public class SimpleEventService implements EventService {

    @PerLogging
    @Override
    public void createEvent() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Create an Event");
    }

    @PerLogging
    @Override
    public void publishEvent() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("publish an Event");
    }

    public void deleteEvent() {
        System.out.println("Delete an Event");
    }
}
```
μ€νκ²°κ³Ό
```java
Create an Event
1021
publish an Event
2010
Delete an Event
```
μ΄μ²λΌ `@PerLogging`μ΄λΈνμ΄μμ΄ λΆμ λ©μλλ§ μ±λ₯μΈ‘μ (Aspect)μ΄ λ κ²μ νμΈν  μ μμ΅λλ€. 

λν μ€νλ§ λΉμΌλ‘ λ±λ‘λ λ©μλμ μ μ©ν μλ μμ΅λλ€. 
```java
@Component
@Aspect
public class PerfAspect {

    @Around("bean(simpleEventService)")
    public Object loObject(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}
```
---
## **ν¬μΈνΈμ»· μ μ**
* @Pointcut(ννμ)
* μ£Όμ ννμ
    * execution
    * @annotation
    * bean
* ν¬μΈνΈμ»· μ‘°ν©
    * &&, ||, !

---
## **μ΄λλ°μ΄μ€ μ μ**
* @Before
```java
@Before("bean(simpleEventService)")
public void hello() {
    System.out.println("Hello");
}
```
* @AfterReturning
* @AfterThrowing
* @Around
---
## **μ λ¦¬νκΈ°**
* AOPλ ν©μ΄μ§ κ΄μ¬μ¬λ₯Ό Aspectλ¦¬λ κ²μΌλ‘ λͺ¨λν νλ νλμ νλ‘κ·Έλλ° κΈ°λ²μλλ€. 
* μ¬λ¬κ°μ§ AOP κ΅¬νμ²΄λ€μ΄ μ‘΄μ¬νμ§λ§, μλ°μμλ AspectJμ μ€νλ§ AOPλ₯Ό λ€ μ μμ΅λλ€. 
* Aspectλ‘ λͺ¨λν ν κ²μ μ μ©νλ λ°©λ²μ μ»΄νμΌ/λ‘λνμ/λ°νμ μΌλ‘ ν¬κ² μΈκ°μ§λ‘ μ μν  μ μμ΅λλ€. 

