# **Pointcut**

AspectJ는 포인트컷을 편리하게 사용하기 위해 AspectJ pointcut expressoion 표현식을 제공합니다. 

* 포인트컷 지시자 : 포인트컷 표현식은 `execution` 같은 포인트컷 지시자로 시작합니다. 줄여서 PCD 라 합니다. 

---
## **포인트컷 지사자의 종류**
포인트컷 지시자의 종류는 다음과 같습니다. 
* `execution` : 메소드 실행 조인 포인트를 매칭합니다. Spring AOP 에서 가장 많이 사용하고 기능도 복잡합니다. 
* `within` : 특정 타입 내의 조인 포인트를 매칭합니다. 
* `args` : 인자가 주어진 타입의 인스턴스인 조인 포인트
* `this` : 스프링 빈 객채(Spring AOP Proxy) 를 대상으로 하는 조인 포인트
* `target` : Target 객채(Spring AOP Proxy가 가르키는 실제 대상)을 대상으로 하는 조인 포인트
* `@target` : 실행 객체의 클래스에 주어진 타입의 어노테이션이 있는 조인 포인트
* `@within` : 주어진 어노테이션이 있는 타입 내 조인 포인트
* `@annotation` : 메소드가 주어진 어노테이션을 가지고 있는 조인 포인트를 매칭
* `@args` : 전달된 실제 인수의 런타임 타입이 주어진 타입의 어느테이션을 갖는 조인 포인트
* `bean` : 스프링 전용 포인트컷 지시자, 빈의 이름으로 포인트컷을 지정

사실상 가장 많이 사용하는 것은 execution 이므로 나머지는 참고사항으로 알아두면 좋을 것 같습니다. 


---
## **`execution`**

execution 문법은 다음과 같습니다. 
```
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern?name-pattern(param-pattern)
 throws-pattern?)

execution(접근제어자? 반환타입 선언타입?메서드이름(파라미터) 예외?)
```
* 메소드 실행 조인 포인트를 매칭
* ? 생략 가능
* `*` 같은 패턴 지정 가능

이를 테스트 하기 위한 코드를 작성 해보겠습니다.

MemberService
```java
public interface MemberService {

  String hello(String param);
}
```
MemberServiceImpl
```java
@ClassAop
@Component
public class MemberServiceImpl implements MemberService{

  @Override
  @MethodAop("test value")
  public String hello(String param) {
    return "ok";
  }

  public String internal(String param) {
    return "ok";
  }
}
```
annotations
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface ClassAop {}
```
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MethodAop {
  String value();
}
```
testCode
```java
@Slf4j
public class ExecutionTest {

  AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
  Method helloMethod;

  @BeforeEach
  public void init() throws NoSuchMethodException {
    helloMethod = MemberServiceImpl.class.getMethod("hello", String.class);
  }


  @Test
  void printMethod() {
    // helloMethod=public java.lang.String hello.springaop.member.MemberServiceImpl.hello(java.lang.String)
    log.info("helloMethod={}", helloMethod);
  }

  @Test
  void exactMatch() {
    // helloMethod=public java.lang.String hello.springaop.member.MemberServiceImpl.hello(java.lang.String)
    pointcut.setExpression("execution(public String hello.springaop.member.MemberServiceImpl.hello(String))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }
}

```
* Test 코드에선 `AspectJExpressionPointcut` 클래스를 사용하여 포인트컷을 하나 생성하였습니다. 
* 그리고 `init()` 메소드를 통해 `helloMethod`에 위에서 만든 MemberServiceImpl 클래스의 `hello()` 메소드 정보를 넣어주었습니다. 
* `printMethod()`를 통해 helloMethod를 찍어보면 주석과 같이 메소드 정보가 나오는 것을 볼 수 있습니다. 
* 이를 `exactMatch()`에서 포인트컷 표현식을 이용해 작성해보고, 이와 일치하는지 테스트하는 코드를 작성했습니다. 

위의 테스트는 통과합니다. 포인트컷 표현식의 매칭 조건은 다음과 같습니다. 

### **매칭 조건**
* 접근제어자?: public (생략가능)
* 반환타입: String
* 선언타입?: hello.aop.member.MemberServiceImpl (생략가능)
* 메서드이름: hello
* 파라미터: (String)
* 예외?: 생략가능

위의 매칭 조건이 모두 일치하기 때문에 통과하게 됩니다. 

또한, 매칭 조건에선 `*`을 사용하여 아무 값이 허용할 수 있으며, 파라미터에서 `..` 을 사용하여 파라미터의 타입과 파라미터 수가 상관없다는 의미로 사용할 수도 있습니다. 

여기서 생략 가능한 부분을 생략하고 모든 포인트컷을 매칭한다면 다음과 같이 작성할 수 있습니다. 
```java
  @Test
  void allMatch() {
    pointcut.setExpression("execution(* *(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }
```

* 또한 메소드 이름 앞 뒤에 `*`을 사용하여 매칭할 수도 있습니다. 
* 패키지로 매칭하기 위해선 `hello.springaop.member.*(1).*(2)` (1)은 타입, (2)는 메소드 이름으로 매칭할 수 있습니다. 
* 패키지에서 `.`,`..` 는 다릅니다. 
    * `.` : 정확하게 해당 위치의 패키지
    * `..` : 해당 위치의 패키지와 그 하위 패키지도 포함
* 타입 매칭도 가능합니다. 
    * 부모 타입의 메소드를 매칭하여도 매칭됩니다. 
    * 다만, 부모타입에 있는 메소드만 매칭이 가능합니다. 자식 클래스에서 확장한 메소드를 부모타입의 메소드와 매칭한다면 매칭이 실패합니다. 
* 파라미터 매칭도 가능하며 규칙은 다음과 같습니다. 
    * `(String)` : 정확하게 String 타입 파라미터
    * `()` : 파라미터가 없어야 한다.
    * `(*)` : 정확히 하나의 파라미터, 단 모든 타입을 허용한다.
    * `(*, *)` : 정확히 두 개의 파라미터, 단 모든 타입을 허용한다.
    * `(..)` : 숫자와 무관하게 모든 파라미터, 모든 타입을 허용한다. 참고로 파라미터가 없어도 된다. 0..* 로 이해하면 된다.
    * `(String, ..)` : String 타입으로 시작해야 한다. 숫자와 무관하게 모든 파라미터, 모든 타입을 허용한다.
        * 예) (String) , (String, Xxx) , (String, Xxx, Xxx) 허용

위의 설명의 예제 코드는 다음과 같습니다. 
<details>
   <summary>코드보기</summary>
   <div markdown="1">

```java
@Slf4j
public class ExecutionTest {

  AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
  Method helloMethod;

  @BeforeEach
  public void init() throws NoSuchMethodException {
    helloMethod = MemberServiceImpl.class.getMethod("hello", String.class);
  }


  @Test
  void printMethod() {
    // helloMethod=public java.lang.String hello.springaop.member.MemberServiceImpl.hello(java.lang.String)
    log.info("helloMethod={}", helloMethod);
  }

  @Test
  void exactMatch() {
    // helloMethod=public java.lang.String hello.springaop.member.MemberServiceImpl.hello(java.lang.String)
    pointcut.setExpression(
        "execution(public String hello.springaop.member.MemberServiceImpl.hello(String))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  @Test
  void allMatch() {
    pointcut.setExpression("execution(* *(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  @Test
  void nameMatch() {
    pointcut.setExpression("execution(* hello(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  @Test
  void nameMatchStar1() {
    pointcut.setExpression("execution(* hel*(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  @Test
  void nameMatchStar2() {
    pointcut.setExpression("execution(* *el*(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  @Test
  void nameMatchFalse() {
    pointcut.setExpression("execution(* nono(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isFalse();
  }

  @Test
  void packageExactMatch1() {
    pointcut.setExpression("execution(* hello.springaop.member.MemberServiceImpl.hello(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  @Test
  void packageExactMatch2() {
    pointcut.setExpression("execution(* hello.springaop.member.*.*(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  @Test
  void packageExactFalse() {
    pointcut.setExpression("execution(* hello.springaop.*.*(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  @Test
  void packageExactMatchSubPackage1() {
    pointcut.setExpression("execution(* hello.springaop.member..*.*(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  // 상상위의 패키지와 매칭한 경우 (..) 사용하여 하위 패키지를 모두 포함하도록 한다. - 성공
  @Test
  void packageExactMatchSubPackage2() {
    pointcut.setExpression("execution(* hello.springaop..*.*(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  // 확장한 클래스 내부에 있는 메소드와 매칭한 경우 - 성공
  @Test
  void typeExactMatch() {
    pointcut.setExpression("execution(* hello.springaop.member.MemberServiceImpl.*(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();

  }

  // 부모 타입의 클래스의 메소드와 매칭한 경우 - 성공
  @Test
  void typeExactMatchSuperType() {
    pointcut.setExpression("execution(* hello.springaop.member.MemberService.*(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  // 자식 클래스의 확장 메소드와 매칭한 경우 - 성공
  @Test
  void typeMatchInternal() throws NoSuchMethodException {
    pointcut.setExpression("execution(* hello.springaop.member.MemberServiceImpl.*(..))");
    Method internalMethod = MemberServiceImpl.class.getMethod("internal", String.class);
    assertThat(pointcut.matches(internalMethod, MemberServiceImpl.class)).isTrue();
  }

  // 부모 타입에 없는 메소드와 매칭한 경우 - 실패
  @Test
  void typeMatchNoSuperTypeMethodFalse() throws NoSuchMethodException {
    pointcut.setExpression("execution(* hello.springaop.member.MemberService.*(..))");
    Method internalMethod = MemberServiceImpl.class.getMethod("internal", String.class);
    assertThat(pointcut.matches(internalMethod, MemberServiceImpl.class)).isFalse();
  }

  // String 타입의 파라미터 허용
  @Test
  void argsMatch() {
    pointcut.setExpression("execution(* *(String))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  // 파라미터가 없어야 함
  // () 타입의 파라미터 허용
  @Test
  void argsMatchNoArgs() {
    pointcut.setExpression("execution(* *())");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isFalse();
  }

  // 정확히 하나의 파라미터 허용, 모든 타입 허용
  // (xxx)
  @Test
  void argsMatchStar() {
    pointcut.setExpression("execution(* *(*))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  // 숫자와 무관하게 모든 파라미터, 모든 타입 허용
  // (), (xxx), (xxx,xxx)
  @Test
  void argsMatchAll() {
    pointcut.setExpression("execution(* *(..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  // String 타입으로 시작, 숫자와 무관하게 모든 파라미터, 모든 타입 허용
  // (String), (String, xxx), (String, xxx, xxx)
  @Test
  void argsMatchComplex() {
    pointcut.setExpression("execution(* *(String, ..))");
    Assertions.assertThat(pointcut.matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }
}
```
   </div>
</details>

---
## **`within`**
* `within` 지시자는 특정 타입 내의 조인 포인트에 대한 매칭을 제한합니다. 해당 타입이 매칭되면 그 안의 메소드(조인 포인트)들이 자동으로 매칭됩니다. 

* within 은 표현식에 부모 타입을 지정할 수 없습니다. 
* 정확하게 타입이 매칭되어야 합니다. 

예제코드는 다음과 같습니다. 
<details>
   <summary>코드보기</summary>
   <div markdown="1">

```java
public class WithinTest {

  AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
  Method helloMethod;

  @BeforeEach
  public void init() throws NoSuchMethodException {
    helloMethod = MemberServiceImpl.class.getMethod("hello", String.class);
  }

  @Test
  void withinExact() {
    pointcut.setExpression("within(hello.springaop.member.MemberServiceImpl)");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
  }

  @Test
  void withinStar() {
    pointcut.setExpression("within(hello.springaop.member.*Service*)");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
  }

  @Test
  void withinSubPackage() {
    pointcut.setExpression("within(hello.springaop..*)");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
  }

  @Test
  @DisplayName("타겟의 타입에만 직접 적용, 인터페이스를 선정하면 안된다.")
  void withinSuperTypeFalse() {
    pointcut.setExpression("within(hello.springaop.member.MemberService)");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isFalse();
  }

  @Test
  @DisplayName("execution은 타입 기반, 인터페이스 선정 가능")
  void executionSuperTypeTrue() {
    pointcut.setExpression("execution(* hello.springaop.member.MemberService.*(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
  }
}

```
   </div>
</details>


---
## **`args`**
* `args`는 인자가 주어진 타입의 인스턴스인 조인 포인트로 매칭합니다. 기본 문법은 execution의 args 부분과 동일합니다. 

다만, 다른 점은 execution은 파라미터 타입이 정확하게 매칭되어야 하며, 클래스에 선언된 정보를 기반으로 판단합니다. 하지만, args는 부모 타입을 허용합니다. args는 실제 넘어온 파라미터 객체의 객체 인스턴스를 보고 판단합니다. 

* `execution(* *(java.io.Serializable))` : 메서드의 시그니처로 판단 (정적) 
* `args(java.io.Serializable)` : 런타임에 전달된 인수로 판단 (동적)

<details>
   <summary>코드보기</summary>
   <div markdown="1">

```java
public class ArgsTest {

  Method helloMethod;

  @BeforeEach
  public void init() throws NoSuchMethodException {
    helloMethod = MemberServiceImpl.class.getMethod("hello", String.class);
  }

  private AspectJExpressionPointcut pointcut(String expression) {
    AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
    pointcut.setExpression(expression);
    return pointcut;
  }

  @Test
  void args() {
    //hello(String)과 매칭
    assertThat(pointcut("args(String)").matches(helloMethod, MemberServiceImpl.class)).isTrue();
    assertThat(pointcut("args(Object)").matches(helloMethod, MemberServiceImpl.class)).isTrue();
    assertThat(pointcut("args()").matches(helloMethod, MemberServiceImpl.class)).isFalse();
    assertThat(pointcut("args(..)").matches(helloMethod, MemberServiceImpl.class)).isTrue();
    assertThat(pointcut("args(*)").matches(helloMethod, MemberServiceImpl.class)).isTrue();
    assertThat(pointcut("args(String,..)").matches(helloMethod, MemberServiceImpl.class)).isTrue();
  }

  /**
   * execution(* *(java.io.Serializable)): 메서드의 시그니처로 판단 (정적)
   * args(java.io.Serializable): 런타임에 전달된 인수로 판단 (동적)
   */
  @Test
  void argsVsExecution() {
    //Args
    assertThat(pointcut("args(String)").matches(helloMethod, MemberServiceImpl.class)).isTrue();
    assertThat(pointcut("args(java.io.Serializable)").matches(helloMethod, MemberServiceImpl.class)).isTrue();
    assertThat(pointcut("args(Object)").matches(helloMethod, MemberServiceImpl.class)).isTrue();

    //Execution
    assertThat(pointcut("execution(* *(String))").matches(helloMethod, MemberServiceImpl.class)).isTrue();
    assertThat(pointcut("execution(* *(java.io.Serializable))") //매칭 실패
        .matches(helloMethod, MemberServiceImpl.class)).isFalse();
    assertThat(pointcut("execution(* *(Object))") //매칭 실패
        .matches(helloMethod, MemberServiceImpl.class)).isFalse();
  }
}

```
   </div>
</details>


---
## **`@target`, `@within`**
* `@target` : 실행 객체의 클래스에 주어진 타입의 어노테이션이 있는 조인 포인트 <br>
* `@within` : 주어진 어노테이션이 있는 타입 내 조인 포인트

즉, `@target(hello.aop.member.annotation.ClassAop)`, `@within(hello.aop.member.annotation.ClassAop)` 로 설정할 경우 `ClassAop`과 같은 어노테이션을 가진 타입으로 AOP 적용 여부를 판단합니다. 

**`@target`** vs **`@within`**
* `@target`은 인스턴스의 모든 메소드를 조인 포인트로 사용
* `@within`은 해당 타입 내에 있는 메소드만 조인 포인트로 사용

즉, `@target`은 부모 클래스의 메소드까지 어드바이스를 모두 적용하고, `@whitin`은 자기 자신의 클래스에 정의된 메소드만 어드바이스를 적용합니다. 

![image](https://user-images.githubusercontent.com/63777714/152966567-4e660178-4e4b-4c45-baca-5ab521ae6b12.png)

<details>
   <summary>코드보기</summary>
   <div markdown="1">

```java
@Slf4j
@Import({AtTargetAtWithinTest.Config.class})
@SpringBootTest
public class AtTargetAtWithinTest {

  @Autowired
  Child child;

  @Test
  void success() {
    log.info("child Proxy={}", child.getClass());
    child.childMethod(); //부모, 자식 모두 있는 메서드
    child.parentMethod(); //부모 클래스만 있는 메서드
  }

  static class Config {

    @Bean
    public Parent parent() {
      return new Parent();
    }

    @Bean
    public Child child() {
      return new Child();
    }

    @Bean
    public AtTargetAtWithinAspect atTargetAtWithinAspect() {
      return new AtTargetAtWithinAspect();
    }
  }

  static class Parent {

    public void parentMethod() {} //부모에만 있는 메서드
  }

  @ClassAop
  static class Child extends Parent {

    public void childMethod() {
    }
  }

  @Slf4j
  @Aspect
  static class AtTargetAtWithinAspect {

    //@target: 인스턴스 기준으로 모든 메서드의 조인 포인트를 선정, 부모 타입의 메서드도 적용
    @Around("execution(* hello.springaop..*(..)) && @target(hello.springaop.member.annotation.ClassAop)")
    public Object atTarget(ProceedingJoinPoint joinPoint) throws Throwable {
      log.info("[@target] {}", joinPoint.getSignature());
      return joinPoint.proceed();
    }

    //@within: 선택된 클래스 내부에 있는 메서드만 조인 포인트로 선정, 부모 타입의 메서드는 적용되지 않음
    @Around("execution(* hello.springaop..*(..)) && @within(hello.springaop.member.annotation.ClassAop)")
    public Object atWithin(ProceedingJoinPoint joinPoint) throws Throwable {
      log.info("[@within] {}", joinPoint.getSignature());
      return joinPoint.proceed();
    }
  }
}
```
   </div>
</details>

* `@target`과 `@within` 지시자는 뒤에서 설명할 파라미터 바인딩에서 함께 사용되기도 합니다. 

> 🔸 주의 🔸 <br>
> <u>**`args`, `@args`, `@target`은 단독으로 사용하면 안됩니다.**</u> 
> `@target`은 실제 객체 인스턴스가 생성되고, 실행될 떄 어드바이스 적용 여부를 확인할 수 있습니다. 따라서, `args`, `@args`, `@target`는 모든 스프링 빈에 AOP 를 적용하려고 시도합니다. 하지만, final과 같은 빈들은 오류가 발생할 수 있기 때문에 최대한 범위를 축소하는 표현식과 함께 사용되어야 합니다.

---
## **`@annotation`, `@args`**
* `@annotation` : 메소드가 주어진 어노테이션을 가지고 있는 조인 포인트를 매칭
    * ex) `@annotation(hello.springaop.member.annotation.MethodAop)`
* `@args` : 전달된 실제 인수에 런타인 타입이 주어진 타입의 어노테이션을 갖는 조인 포인트
    * 즉, 전달된 인수의 런타임 타입에 특정 어노테이션이 있는 경우에 매칭이 됩니다. 

@annotation의 예제를 살펴보겠습니다. 
`@annotation(hello.springaop.member.annotation.MethodAop)`와 같이 정의해놓으면 괄호 안에 있는 어노테이션을 가지고 있는 조인 포인트를 매칭합니다. 

```java
@ClassAop
@Component
public class MemberServiceImpl implements MemberService{

  @Override
  @MethodAop("test value")
  public String hello(String param) {
    return "ok";
  }

  public String internal(String param) {
    return "ok";
  }
}
```

위 코드에선 `@MethodAop` 어노테이션을 갖고있는 `hello()` 메소드가 매칭이 됩니다. 

적용된 것을 테스트한 코드는 다음과 같습니다. 
<details>
   <summary>코드보기</summary>
   <div markdown="1">
   
```java
@Slf4j
@Import(AtAnnotatopnTest.AtAnnotationAspect.class)
@SpringBootTest
public class AtAnnotatopnTest {

  @Autowired
  MemberService memberService;

  @Test
  void success() {
    log.info("memberService Proxy={}", memberService.getClass());
    memberService.hello("helloA");
  }

  @Slf4j
  @Aspect
  static class AtAnnotationAspect {

    @Around("@annotation(hello.springaop.member.annotation.MethodAop)")
    public Object doAtAnnotation(ProceedingJoinPoint joinPoint) throws Throwable {
      log.info("[@annotation] {}", joinPoint.getSignature());
      return joinPoint.proceed();
    }
  }
}
```


테스트 결과

![image](https://user-images.githubusercontent.com/63777714/153146041-444d8443-ed7d-4c32-a920-18a9ec71e54f.png)

 </div>
</details>

---
## **`bean`**
* `bean` : 스프링 전용 포인트컷 지시자, 빈의 이름으로 지정합니다. 
* 스프링 빈의 이름으로 AOP 적용 여부를 지정하며, 스프링 내부에서만 사용할 수 있는 지시자입니다. 
* `bean(orderService) || bean(*Repository)` 와 같이 사용할 수 있습니다. 

---
참고
- 스프링 핵심 원리-고급편 (인프런_김영한님)