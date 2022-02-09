# **Spring AOP 구현**

## **의존성 추가**

Spring Boot 환경에서 Spring AOP를 사용하기 위해선 의존성을 먼저 추가해주어야 합니다. 
```
implementation 'org.springframework.boot:spring-boot-starter-aop'
```

`@Aspect` 어노테이션을 사용하려면 `@EnableAspectJAutoProxt` 를 Spring 설정에 추가해야 하지만, Spring Boot는 자동으로 추가해주기 때문에 Boot 사용시 따로 설정을 해주지 않아도 됩니다. 

---
## **`@Aspect`, `@Around` 를 사용하여 AOP 구현해보기**


우선 구현에 필요한 Repository 와 Service 코드를 만들겠습니다. 

```java
@Slf4j
@Repository
public class OrderRepository {

  public String save(String itemId) {
    log.info("[orderRepository] 실행");
    //저장 로직
    if (itemId.equals("ex")) {
      throw new IllegalStateException("예외 발생!");
    }
    return "ok";
  }
}
```
* OrderReposiroty 코드입니다. "ex"가 들어오면 예외를 발생시키고, 그 외에는 정상적으로 로직을 수행합니다. 
```java
@Slf4j
@Service
public class OrderService {

  private final OrderRepository orderRepository;

  public OrderService(OrderRepository orderRepository) {
    this.orderRepository = orderRepository;
  }

  public void orderItem(String itemId) {
    log.info("[orderService] 실행");
    orderRepository.save(itemId);
  }
}
```
* OrderRepository를 주입받아 save() 메소드를 호출하는 로직입니다. 

자 이제 AOP 를 적용하는 설정파일을 만들겠습니다. 
```java
@Slf4j
@Aspect
public class AspectV1 {

  @Around("execution(* hello.springaop.order..*(..))")
  public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable{
    log.info("[log] {}", joinPoint.getSignature()); // JoinPoint 시그니처
    return joinPoint.proceed();
  }
}

```
* 클래스에 `@Aspect` 어노테이션을 붙여 자동 프록시 생성기가 읽을 수 있도록 합니다. 
* `@Around` 어노테이션의 메소드인 `doLog()` 가 어드바이스가 됩니다. 
* `execution`을 사용하여 포인트컷을 지정하였습니다. 
* `execution`을 통해 지정해준 범위에 OrderService, OrderRepository 가 포함되어 있어 이 두 개의 클래스는 어드바이스 대상이 됩니다. 

다음은 테스트할 코드입니다. 
```java
@Slf4j
@SpringBootTest
@Import(AspectV1.class)
public class AopTest {

  @Autowired
  OrderService orderService;

  @Autowired
  OrderRepository orderRepository;

  @Test
  void aopInfo() {
    log.info("isAopProxy, orderService={}", AopUtils.isAopProxy(orderService));
    log.info("isAopProxy, orderRepository={}", AopUtils.isAopProxy(orderRepository));
  }

  @Test
  void success() {
    orderService.orderItem("itemmA");
  }

  @Test
  void exception() {
    assertThatThrownBy(() -> orderService.orderItem("ex"))
        .isInstanceOf(IllegalStateException.class);
  }
}

```
* AopUtils 를 사용하여 프록시가 생성이 되었는지 확인합니다. 
* Import를 사용하여 스프링 빈 설정파일을 추가하였습니다. 
* success() 를 실행한 결과는 다음과 같이 나옵니다. 
```
[log] void hello.aop.order.OrderService.orderItem(String)
[orderService] 실행
[log] String hello.aop.order.OrderRepository.save(String)
[orderRepository] 실행
```
---
## **포인트 컷 분리해보기**
이전 예제에선 `@Around` 를 통해 직접적으로 포인트 컷을 적용해줬지만 `@Pointcut` 어노테이션을 통해 포인트컷을 분리할 수도 있습니다. 

```java
@Slf4j
@Aspect
public class AspectV2 {

  // hello.springaop.order 패키지와 하위 패키지
  @Pointcut("execution(* hello.springaop.order..*(..))") // pointcut expression
  private void allOrder() {} // pointcut signature (메소드 이름 + 파라미터)

  @Around("allOrder()")
  public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable{
    log.info("[log] {}", joinPoint.getSignature()); // JoinPoint 시그니처
    return joinPoint.proceed();
  }
}

```
* 이처럼 @Pointcut에 포인트컷 표현식인 execution을 사용하면 됩니다. 
* `메소드 이름 + 파라미터`를 포인트컷 시그니처라고 합니다. 
* 메소드의 반환 타입은 void 여야 합니다. 
* 코드 내용은 비워둡니다. 
* 포인트컷을 여러개 사용하고 싶다면 `&&`(and) , `||`(or), `!`(not) 과 함께 사용할 수 있습니다. 

---
## **포인트 컷 참조**
포인트 컷을 공통적으로 사용하기 위해 외부로 빼는 경우도 있습니다. 이 경우엔 포인트컷의 접근 제어자를 public으로 설정해야하며, 사용하고자 하는 포인트컷의 `패키지명.클래스명.포인트컷 시그니처`을 사용하면 참조하여 사용할 수 있습니다. 

Pointcuts.class
```java
package hello.springaop.order.aop;

import org.aspectj.lang.annotation.Pointcut;

public class Pointcuts {

  // hello.springaop.order 패키지와 하위 패키지
  @Pointcut("execution(* hello.springaop.order..*(..))") // pointcut expression
  public void allOrder() {} // pointcut signature (메소드 이름 + 파라미터)

  // 클래스 이름 패턴이 *Service
  @Pointcut("execution(* *..*Service.*(..))")
  public void allService() {}

  // allOrder && allService
  @Pointcut("allOrder() && allService()")
  public void orderAndService() {}


}
```
AspectV4Pointcut.class --> 포인트컷을 적용하고자 하는 Aspect 클래스
```java
@Slf4j
@Aspect
public class AspectV4Pointcut {

  @Around("hello.springaop.order.aop.Pointcuts.allOrder()")
  public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable {
    log.info("[log] {}", joinPoint.getSignature()); // JoinPoint 시그니처
    return joinPoint.proceed();
  }

  // hello.springaop.order 패키지와 하위 패키지이면서 클래스 이름 패턴이 *Service 인 것
  @Around("hello.springaop.order.aop.Pointcuts.orderAndService()")
  public Object doTransaction(ProceedingJoinPoint joinPoint) throws Throwable {

    try {
      log.info("[트랜잭션 시작] {}", joinPoint.getSignature());
      Object result = joinPoint.proceed();
      log.info("[트랜잭션 커밋] {}", joinPoint.getSignature());
      return result;
    } catch (Exception e) {
      log.info("[트랜잭션 롤백] {}", joinPoint.getSignature());
      throw e;
    } finally {
      log.info("[리소스 릴리즈] {}", joinPoint.getSignature());
    }
  }
}

```
* 다음과 같이 패키지명을 포함한 클래스 이름과 포인트컷 시그니처를 사용하면 됩니다. 
* 포인트컷을 여러 어드바이스에서 함께 사용할 때 효과적입니다. 

---
## **어드바이스 순서**
어드바이스는 기본적으로 순서를 보장하지 않습니다. 만약 순서를 보장하고싶다면 `@Order` 어노테이션을 사용하면 됩니다. 하지만 이것은 `@Aspect` 단위로 적용할 수 있습니다. 즉, 하나의 `@Aspect` 클래스에 여러 어드바이스가 존재하면 이는 순서를 보장할 수 없습니다. 따라서, 순서를 보장하고 싶다면 별도의 클래스로 분리해주어야 합니다.

```java
@Slf4j
public class AspectV5Order {

  @Aspect
  @Order(2)
  public static class LogAspect{

    @Around("hello.springaop.order.aop.Pointcuts.allOrder()")
    public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable {
      log.info("[log] {}", joinPoint.getSignature()); // JoinPoint 시그니처
      return joinPoint.proceed();
    }

  }

  @Aspect
  @Order(1)
  public static class TxAspect {
    // hello.springaop.order 패키지와 하위 패키지이면서 클래스 이름 패턴이 *Service 인 것
    @Around("hello.springaop.order.aop.Pointcuts.orderAndService()")
    public Object doTransaction(ProceedingJoinPoint joinPoint) throws Throwable {

      try {
        log.info("[트랜잭션 시작] {}", joinPoint.getSignature());
        Object result = joinPoint.proceed();
        log.info("[트랜잭션 커밋] {}", joinPoint.getSignature());
        return result;
      } catch (Exception e) {
        log.info("[트랜잭션 롤백] {}", joinPoint.getSignature());
        throw e;
      } finally {
        log.info("[리소스 릴리즈] {}", joinPoint.getSignature());
      }
    }

  }
}

```

위의 코드와 같이 클래스 안에서 분리해주어도 되고, 외부 클래스로 분리해주어도 됩니다. `@Aspect` 단위로 순서가 보장되기 때문에 클래스 단위로 분리해주어야 합니다. 

---
## **어드바이스 종류**
* `@Around` : 메소드 호출 전후에 수행, 가장 강력한 어드바이스, 조인 포인트 실행 여부 선택, 반환 값 변환, 예외 변환 등이 가능
* `@Before` : 조인 포인트 실행 이전에 실행
* `@AfterReturning` : 조인 포인트가 정상 완료후 실행
* `@AfterThrowing` : 메소드가 예외를 던지는 경우 실행
* `@After` : 조인 포인트가 정상 또는 예외에 관계 없이 실행 (finally)

```java
@Slf4j
@Aspect
public class AspectV6Advice {

  @Before("hello.springaop.order.aop.Pointcuts.orderAndService()")
  public void doBefore(JoinPoint joinPoint) {
    log.info("[before] {}", joinPoint.getSignature());
  }

  @AfterReturning(value = "hello.springaop.order.aop.Pointcuts.orderAndService()", returning = "result")
  public void doReturn(JoinPoint joinPoint, Object result) {
    log.info("[return] {} return {}", joinPoint.getSignature(), result);
  }

  @AfterThrowing(value = "hello.springaop.order.aop.Pointcuts.orderAndService()", throwing = "ex")
  public void doThrowing(JoinPoint joinPoint, Exception ex) {
    log.info("[ex] {} message {}", joinPoint.getSignature(), ex);
  }

  @After(value = "hello.springaop.order.aop.Pointcuts.orderAndService()")
  public void doAfter(JoinPoint joinPoint) {
    log.info("[after] {} ", joinPoint.getSignature());
  }
}

```

test code
```java
@Import({AspectV6Advice.class})
public class AopTest {

  @Autowired
  OrderService orderService;

  @Autowired
  OrderRepository orderRepository;

  @Test
  void success() {
    orderService.orderItem("itemmA");
  }

```
결과는 다음과 같습니다.
```
[before] void hello.springaop.order.OrderService.orderItem(String)
[orderService] 실행
[orderRepository] 실행
[return] void hello.springaop.order.OrderService.orderItem(String) return null
[after] void hello.springaop.order.OrderService.orderItem(String) 
```

사실상 `@Around`를 제외한 어드바이스들은 `@Around`가 할 수 있는 일의 일부만 제공하는 것입니다. 
```java
  @Around("hello.springaop.order.aop.Pointcuts.orderAndService()")
  public Object doTransaction(ProceedingJoinPoint joinPoint) throws Throwable {

    try {
      // @Before
      log.info("[트랜잭션 시작] {}", joinPoint.getSignature());
      Object result = joinPoint.proceed();
      // @AfterReturning
      log.info("[트랜잭션 커밋] {}", joinPoint.getSignature());
      return result;
    } catch (Exception e) {
      // @AfterThrowing
      log.info("[트랜잭션 롤백] {}", joinPoint.getSignature());
      throw e;
    } finally {
      // @After
      log.info("[리소스 릴리즈] {}", joinPoint.getSignature());
    }
  }

```

`@Around`가 적용된 코드에 주석으로 어드바이스를 작성했습니다. 이를 보면 `@Around`가 하는 일의 일부를 제공한다는 것을 이해할 수 있습니다. 

> 🔍그렇다면 왜 기능을 쪼개놓았을까요?
> * 모든 어드바이스는 `org.aspectj.lang.JoinPoint`를 첫번째 파라미터에 사용할 수 있습니다.(생략도 가능). 하지만, `@Around`는 `ProceedingJoinPoint`를 사용해야 합니다. 
> * `ProceedingJoinPoint`는 `JoinPoint`의 하위 타입으로 다음 어드바이스나 타겟을 호출하는 `proceed()`라는 메소드가 추가된 확장버전입니다. 
> * 따라서, `@Around`를 사용하려면 `ProceedingJoinPoint.proceed()`를 사용하여 개발자가 직접 어드바이스나 타겟을 호출해주어야합니다. 
> * 하지만, 개발자가 깜빡하여 `proceed()`를 호출해주지 않는다면 이는 치명적인 문제로 이어집니다. 즉, 넓은 기능을 제공하지만 실수할 가능성이 많습니다. 
> * `@Around` 를 제외한 나머지 어드바이스는 이런 타겟의 기능 호출에 대한 코드를 작성하지 않아도 자동으로 타겟을 호출해주며, 코드를 작성한 의도가 명확하게 드러나기 때문에 쪼개어 사용합니다. 
    

<br>

그럼 어드바이스에 대해 하나씩 뜯어서 살펴보도록 하겠습니다. 

**@Before** : 조인 포인트 실행 전
```java
  @Before("hello.springaop.order.aop.Pointcuts.orderAndService()")
  public void doBefore(JoinPoint joinPoint) {
    log.info("[before] {}", joinPoint.getSignature());
  }

```
* `@Around` 와는 다르게 작업 흐름을 변경할 수 없습니다. 
* `@Around` 는 `ProceedingJoinPoint.proceed()` 를 호출해야 다음 대상이 호출되는 반면에 `@Before` 는 `ProceedingJoinPoint.proceed()` 자체를 사용하지 않습니다. 메서드 종료시 자동으로 다음 타켓이 호출되며 예외가 발생하면 다음 코드가 호출되지는 않습니다.

<br>

**@AfterReturning** : 메소드 실행이 정상적으로 반환될 때 실행
```java
  @AfterReturning(value = "hello.springaop.order.aop.Pointcuts.orderAndService()", returning = "result")
  public void doReturn(JoinPoint joinPoint, Object result) {
    log.info("[return] {} return {}", joinPoint.getSignature(), result);
  }

```
* `returning` 속성에 사용된 이름은 어드바이스 메소드에 매개변수 이름과 일치해야 합니다.
* `returning` 에 지정된 타입의 값을 반환하는 메소드만 대상으로 실행됩니다. (부모타입 지정시 자식타입 인정)
* `@Around`와 다르게 반환되는 객체를 변경할 수 없습니다. 반환 객체를 조작할 수는 있습니다. 

<br>

**@AfterThrowing** : 메소드 실행이 예외를 던져서 종료될 때 실행
```java
  @AfterThrowing(value = "hello.springaop.order.aop.Pointcuts.orderAndService()", throwing = "ex")
  public void doThrowing(JoinPoint joinPoint, Exception ex) {
    log.info("[ex] {} message {}", joinPoint.getSignature(), ex);
  }
```
* `throwing` 속성에 사용된 이름은 어드바이스 메서드의 매개변수 이름과 일치해야 합니다.
* `throwing` 절에 지정된 타입과 맞은 예외를 대상으로 실행됩니다. (부모 타입을 지정하면 모든 자식 타입은 인정)

<br>

**@After**
* 메소드 실행이 종료되면 실행 (finally 개념)
* 정상종료든 예외 발생이든 무조건 호출
* 일반적으로 리소스를 해제하는 경우에 사용

<br>

**@Around**
* 메소드 실행 전후로 작업을 실행
* 가장 강력한 어드바이스
    * 조인 포인트 실행 여부 선택 joinPoint.proceed() 호출 여부 선택
    * 전달 값 변환: joinPoint.proceed(args[])
    * 반환 값 변환
    * 예외 변환
    * 트랜잭션 처럼 try ~ catch~ finally 모두 들어가는 구문 처리 가능
* 어드바이스의 첫 번째 파라미터는 ProceedingJoinPoint 를 사용
* `proceed()` 를 통해 대상을 실행
* `proceed()` 를 여러번 실행할 수도 있음(재시도)

<br>

### **어드바이스의 실행 순서**

![image](https://user-images.githubusercontent.com/63777714/152943879-161b0912-ae3a-44c4-bd66-4780d186eb54.png)

* Spring 5.2.7 버전부터 동일한 `@Aspect` 안에서 동일한 조인포인트의 우선순위입니다. 
* `@Around` -> `@Before` ->  `@After` ->  `@AfterReturning` ->  `@AfterThrowing`
* 어드바이스가 적용되는 순서는 다음과 같지만 호출 순서와 리턴 순서는 반대입니다. 

---
참고
- 스프링 핵심 원리-고급편 (인프런_김영한님)