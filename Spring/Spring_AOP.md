> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **스프링 AOP (Aspect Oriented  Programming)**

**AOP**는 **Aspect Oriented  Programming**의 약자로 관점 지향 프로그래밍입니다. 흩어진 관심사(Aspect)를 모듈화 할 수 있는 프로그래밍 기법이라고 생각하면 됩니다. 

**코드는 핵심관심사와 횡단 관심사**로 이루어져 있으며 핵심관심사는 모듈별로 다르지만 횡단 관심사는 모듈별로 반복되어 중복해서 나타나는 부분입니다. 

AOP에서 각 관점을 기준으로 로직을 모듈화한다는 것은 코드들을 부분적으로 나누어서 모듈화하겠다는 것 입니다. 이때, 소스코드상에서 다른 부분에 계속 반복해서 쓰이는 코드들을 발견할 수 있는데 이것을 **흩어진 관심사(Crosscutting Concerns)** 라고 합니다. 

![image](https://user-images.githubusercontent.com/63777714/141775047-29af6433-887c-490a-a091-1a55e8437b7e.png)

위의 사진들과 같이 흩어진 관심사를 Aspect로 모듈화하고 **핵심적인 비즈니스 로직에서 분리하여 재사용하겠다는 것이 AOP의 취지**입니다. 

---
## **AOP 주요 개념**
* **Aspect** : 흩어진 관심사를 모듈화 한 것. 즉, 각각의 모듈을 의미함
* **Target** : 적용이 되는 대상
* **Advice** : 해야할 일 들
* **Join Point** : Advice가 적용될 위치, 끼어들 수 있는 지점. 예를들면, 메소드 실행 시점,생성자 호출하기 직전, 필드에 접근하기 직전... 등 다양한 시점에 적용 가능
* **Point Cut** : **Join Point의 구체화한 것**으로 'A클래스에 B메소드 호출할 때 적용해라'처럼 구체적으로 Advice가 실행될 지점을 정함 ( 어디에 적용해야 하는지 )

---
## **AOP 구현체**
 * JAVA
    * AspectJ
    * 스프링 AOP
---
## **AOP 적용 방법**
* **컴파일** (.java -> .class) : 컴파일이 되는 시점에 적용하는 방법
* **로딩 타임** : 컴파일을 거친 클래스파일을 로딩하는 시점에 적용하는 방법
* **런타임** : Bean을 만들 때(A라는 클래스를 빈으로 등록하는 작업은 런타임시에 일어남) 적용하는 방법 

---
## **스프링 AOP 특징**
* **프록시 기반의 AOP 구현체**, 접근제어 및 부가기능을 추가하기 위해 프록시 객체를 사용함
* **스프링 Bean에만 AOP를 적용**할 수 있음
* 모든 AOP 기능을 제공하는 것이 목적이 아니라, 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제에 대한 해결책을 제공하는 것이 목적, 여기서 흔한 문제라는 것은 중복코드, 프록시 클래스 작성의 번거로움 등을 말합니다. 

<details markdown="1">
<summary>프록시 패턴</summary>

### **프록시 패턴**

![image](https://user-images.githubusercontent.com/63777714/141782732-d95f202b-bd45-449e-9aef-a98a696e08ae.png)

 * 왜 AOP는 프록시 패턴을 사용하는가?
    * 기존 코드 변경 없이 접근 제어 또는 부가기능 추가 가능


프록시 패턴을 사용하여 기존의 코드를 건드리지 않고 성능측정을 해보도록 하겠습니다. 
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

프록시 패턴 적용
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
@Primary // 같은 타입의 빈이 여러가지 일 때 그 중 하나를 선택하는 어노테이션
@Service
public class ProxySimpleEventService implements EventService {

    // Proxy의 경우 Subject의 빈을 주입받아서 사용해야합니다.
    // EventService로 등록을 해도 되지만 변수명을 Subject 명으로 사용해야 합니다.
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
실행결과
```java
Create an Event
1016
publish an Event
2013
Delete an Event

```

프록시 클래스 파일을 만들어서 기존 소스코드에 변경없이 성능을 테스트 하였습니다. 하지만, 이 방법에도 다음과 같은 문제점이 있습니다. 
* 매번 프록시 클래스를 작성해야 하는가?
* 여러 클래스 여러 메소드에 적용하려면?
* 객체들 관계도 복잡

위의 문제를 해결하기 위해 등장한 것이 **스프링 AOP**입니다. 


</details>

---

## **스프링 AOP : @AOP**

어노테이션 기반의 @AOP 

스프링 @AOP 을 사용하려면 의존성을 다음과 같이 추가해야 합니다. 
```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

`@Aspect` 어노테이션을 붙여 Aspect를 나타내는 클래스 임을 명시하고, `@Component`를 붙여 빈으로 등록합니다. 
```java
@Component
@Aspect
public class PerfAspect {

    @Around("execution(* com.example..*.EventService.*(..))") // 메소드를 감싸는 형태로 적용이 됨
    public Object loObject(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}

```
`@Around` 어노테이션은 메소드를 감싸는 형태로 적용이 되며, Advice를 실행하겠다는 의미가 됩니다. `@Around()`괄호 안에는 PointCut의 이름을 줄 수도 있고, pointCut을 직접 정의할 수도 있습니다. 

즉, `loObject()` 라는 `Advice`를 어떻게 적용할 것인가? 바로 `@Around`로 적용을 하겠다는 것 입니다. `@Around`는 메소드 호출 전, 후 , 에러발생시 적용도 할 수 있습니다. 

`execution(* com.example..*.EventService.*(..))`는 com.example 하위의 패키지 경로의 `EventService` 객체의 모든 메소드에 이 Aspect를 적용하겠다는 뜻입니다. 

적용시킬 코드입니다. 
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
실행결과
```java
Create an Event
1021
publish an Event
2010
Delete an Event
0
```
createEvent(), publishEvent(), deleteEvent() 모두 성능측정이 적용된 것을 확인할 수 있습니다. 

하지만 EventService 클래스 내의 deleteEvent()는 성능측정으로 하고싶지 않은 경우엔 어떻게 해야할까요?

바로 **어노테이션을 만들어서 어노테이션 기반으로 PointCut을 지정하여 사용**하는 방법이 있습니다. 
```java
@Retention(RetentionPolicy.CLASS) // 이 어노테이션 정보를 어느정도 까지 유지할 것인가 기본값은 CLASS
@Target(ElementType.METHOD) // 메소드가 타겟임
@Documented
public @interface PerLogging {

}
```
```java
@Component
@Aspect
public class PerfAspect {

    //위의 방법은 적용을 원치않는 deleteEvent도 적용이 되어버림 이런경우엔 어노테이션으로 활용하는 것이 좋음
    @Around("@annotation(PerLogging)")
    public Object loObject(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}

```
성능측정을 하고싶은 메소드에만 만들어놓은 어노테이션을 적용시키면 됩니다. 
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
실행결과
```java
Create an Event
1021
publish an Event
2010
Delete an Event
```
이처럼 `@PerLogging`어노테이션이 붙은 메소드만 성능측정(Aspect)이 된 것을 확인할 수 있습니다. 

또한 스프링 빈으로 등록된 메소드에 적용할수도 있습니다. 
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
## **포인트컷 정의**
* @Pointcut(표현식)
* 주요 표현식
    * execution
    * @annotation
    * bean
* 포인트컷 조합
    * &&, ||, !

---
## **어드바이스 정의**
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
## **정리하기**
* AOP는 흩어진 관심사를 Aspect리는 것으로 모듈화 하는 하나의 프로그래밍 기법입니다. 
* 여러가지 AOP 구현체들이 존재하지만, 자바에서는 AspectJ와 스프링 AOP를 들 수 있습니다. 
* Aspect로 모듈화 한 것을 적용하는 방법은 컴파일/로드타임/런타임 으로 크게 세가지로 정의할 수 있습니다. 

