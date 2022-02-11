# **Junit5**

![image](https://user-images.githubusercontent.com/63777714/153351754-10997191-d2c9-4f89-8a60-09db2dd3032e.png)

Junit 5는 테스팅 프레임워크로 위의 사진처럼 총 3가지의 모듈로 구성됩니다. 
* Platform: 테스트를 실행해주는 런처 제공. TestEngine API 제공
* Jupiter: TestEngine API 구현체로 JUnit 5를 제공
* Vintage: JUnit 4와 3을 지원하는 TestEngine 구현체

기본적인 어노테이션은 다음과 같습니다. 
* `@Test`	테스트임을 나타내는 어노테이션
* `@BeforeAll` , `@AfterAll` : 클래스 내의 테스트 실행 전 후에 한번만 실행
* `@BeforeEach`, `@AfterEach` : @Test 어노테이션이 달린 테스트 전/후에 매번 실행
* `@Disabled` : 테스트 클래스 또는 테스트 메서드를 비활성화하는 데 사용

<br>

## **JUnit 5 `Assertion`**
Assertion 클래스는 테스트 결과를 검증하기 위한 메소드를 제공합니다. 

대표적인 것만 살펴보겠습니다. 
|--|--|
|메소드명|설명|
|--|--|
|`aasertNotNull(actual)`|해당 값이 Null인지를 검사해준다.|
|`aasertEquals(expect,actual,message)`|aasertEquals는 기본적으로 기대 값, 실제 값, 메시지 세개의 값을 매개변수로 받는다.<br> 메세지는 람다식으로 표현이 가능하며 에러가 발생한 경우에만 문자열 연산을 수행하기 때문에 연산 비용이 큰 경우엔 람다식이 성능상 이점이 있다.(Lazy Evaluation)|
|`assertTrue(actual)`|매개변수의 조건이 참인지 확인할 떄 사용한다.|
|`assertAll(executables…)`|함수형 인터페이스를 매개변수로 받을 수 있다. <br> 보통 assertion을 이용한 검증을 여러개 작성하면 하나라도 테스트가 통과하지 못하면 다음테스트를 진행하지 않는다. `assertAll()` 메소드를 사용하여 검증들을 묶어서 한 번에 처리가 가능하다. |



<br>

## **JUnit 5 `@TestInstance`**
JUnit은 기본적으로 테스트를 실행할 때마다 테스트의 인스턴스를 생성합니다. 테스트간의 의존성을 낮추기 위해서 그렇게 생성이 된다고 합니다. 

```java
@Slf4j
class TestInstanceTest {

  int value = 1;

  @Test
  void create() {
    log.info("create 실행, value={}", value++);
    log.info("thisInstance={}", this);
  }

  @Test
  void create1() {
    log.info("create1 실행, value={}", value++);
    log.info("thisInstance={}", this);
  }

}
```
위와 같은 코드가 있습니다. `int value= 1`은 create 와 craete1 모두 사용하는 변수이므로 어디선가 값이 2가 나와야한다고 생각할 수 있습니다. 하지만 막상 값을 출력해보면 다음과 같이 나옵니다. 

![image](https://user-images.githubusercontent.com/63777714/153392752-bf88802d-1b55-416e-8ddf-32d663378b75.png)

어느곳에서 실행하든 전부 1로 출력이 됩니다. this로 로그를 남겨서 본 것을 확인하면 새로운 인스턴스인 것을 확인할 수 있습니다. 

이것이 JUnit 4 까지의 기본 전략이었습니다. JUnit 5 에서는 이렇게 매번 인스턴스를 새로 만들어 사용하는 전략을 변경하는 방법이 추가 되었습니다. 

즉, 클래스 당 클래스의 인스턴스를 한 번만 만들어 사용할 수 있습니다. 이러면 조금이라도 성능적인 장점이 생기고, 제약이 느슨해진다는 장점이 있습니다. 

* `@TestInstance(Lifecycle.PER_CLASS)`
    * 이 태그를 추가하면 클래스당 하나의 인스턴스를 사용합니다.
    * 경우에 따라, 테스트 간에 공유하는 모든 상태를 `@BeforeEach` 또는 `@AfterEach` 에서 초기화할 필요가 있습니다. 
    * `@BeforeAll` 과 `@AfterAll` 을 인스턴스 메소드 또는 인터페이스에 정의한 default 메소드로 정의할 수도 있습니다. (static을 사용하지 않아도 됩니다.)


예제로 살펴보겠습니다. 
```java
@Slf4j
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
class TestInstanceTest {

  int value = 1;

  @BeforeAll
  void beforeAll() {
    log.info("beforeAll 실행");
  }

  @AfterAll
  void afterAll() {
    log.info("afterAll 실행");
  }

  @Test
  void create() {
    log.info("create 실행, value={}", value++);
    log.info("thisInstance={}", this);
  }

  @Test
  void create1() {
    log.info("create1 실행, value={}", value++);
    log.info("thisInstance={}", this);
  }

}
```
위의 코드와 같이 BeforeAll 과 AfterAll을 static으로 정의해주지 않았습니다. 이를 실행한 결과를 보면 다음과 같이 나옵니다. 

![image](https://user-images.githubusercontent.com/63777714/153394300-2643f8c9-8ae4-41fd-a6f0-47b39f24b470.png)

먼저 실행된 메소드가 value 값이 1 나중에 실행된 메소드가 value 값이 2 가 나와 인스턴스를 공유한다는 것을 알 수 있습니다. 

<br>

## **JUnit 5 순서**
JUnit은 하나의 클래스안에 순서대로 작성된 메소드를 호출할 때 JUnit 내부에서 구현된 내부 로직에 의해 호출됩니다. 이는 순서가 보장되지 않으며, 내부로직에 의해 매번 같은 순서로 호출된다고 생각할 수 있지만 이는 순서가 보장되어야 하는 테스트가 존재할 때 실패원인이 될 수 있습니다. 

따라서 순서가 보장되어야 하는 테스트를 작성할 때, (예를 들어 회원 가입을 진행하고 , 그 가입한 회원의 비밀번호 검사를 하고, 주소지 추가를 하는 경우) 순서를 보장하고 싶으면 명시적으로 순서를 기입해주는 방법을 사용하면 됩니다. 

* `@TestInstance(Lifecycle.PER_CLASS)`와 함께 `@TestMethodOrder`를 사용할 수 있습니다. 
다음과 같은 코드가 있다면 결과는 `@Order`에 작성한 순서대로 출력됩니다. 


```java
@Slf4j
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
@TestMethodOrder(OrderAnnotation.class)
class TestInstanceTest {

  int value = 1;

  @BeforeAll
  void beforeAll() {
    log.info("beforeAll 실행");
  }

  @AfterAll
  void afterAll() {
    log.info("afterAll 실행");
  }

  @Order(2)
  @Test
  @DisplayName("스터디 만들기 fast")
  void create() {
    log.info("create 실행, value={}", value++);
    log.info("thisInstance={}", this);
  }

  @Order(1)
  @Test
  void create1() {
    log.info("create1 실행, value={}", value++);
    log.info("thisInstance={}", this);
  }

}
```
결과

![image](https://user-images.githubusercontent.com/63777714/153397298-e2a1260c-eb7f-4ac6-a8ff-396cea0b1879.png)


## **JUnit 설정하기**
JUnit 의 설정파일을 적용할 수 있습니다. 클래스패스 루트 `(src/test/resources/)`에 넣어두면 적용됩니다. 

* 이전에 적용했던 `@TestInstance(Lifecycle.PER_CLASS)`을 설정파일로 등록하려면 다음과 같이 적어주면 됩니다. 
```xml
 junit.jupiter.testinstance.lifecycle.default = per_class 
```
* 이외에도 많은 설정을 적용할 수 있습니다. 
```shell
# 확장팩 자동 감지 기능
junit.jupiter.extensions.autodetection.enabled = true
# @Disabled 무시하고 실행하기
junit.jupiter.conditions.deactivate = org.junit.*DisabledCondition
# 테스트 이름 표기 전략 설정
junit.jupiter.displayname.generator.default = \ # \는 줄바꿈을 의미
org.junit.jupiter.api.DisplayNameGenerator$ReplaceUnderscores

```


## **JUnit 5 확장모델 및 사용방법**
JUnit 4 에서의 확장 모델은 `@RunWith(Runner)`, `TestRule`. `MethodRule`.. 등 확장 방법이 여러개로 나뉘어져 있었습니다. JUnit 5 에서는 확장 방법이 `Extention` 하나로 모두 가능합니다. 훨씬 편리해졌습니다. 

<a href="https://junit.org/junit5/docs/current/user-guide/#extensions">확장 방법 공식문서 보기</a>

확장 등록 방법은 총 세가지로 볼 수 있습니다. 
1. 선언적 방법 `@ExtendWith`
2. 프로그래밍 등록 `@RegisterExtension`
3. 자동 등록 자바 ServiceLoader 이용

우선 선언적인 방법부터 예제로 살펴보겠습니다. 
* 빠른 테스트는 `@FastTest` 어노테이션으로 등록한다. 
* 느린 테스트는 `@SlowTest` 어노테이션으로 등록한다. 
* 일정 시간을 초과하는 테스트에는 `@SlowTest` 로 등록하라는 메세지를 출력한다. 

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Test
@Tag("fast")
public @interface FastTest {

}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Test
@Tag("slow")
public @interface SlowTest {

}
```
```java
@Slf4j
public class FindSlowTestExtension implements BeforeTestExecutionCallback,
    AfterTestExecutionCallback {

  private static final Long THRESHOLD = 1000L;

  @Override
  public void beforeTestExecution(ExtensionContext context) throws Exception {
    Store store = getStore(context);
    store.put("START_TIME", System.currentTimeMillis());
  }

  @Override
  public void afterTestExecution(ExtensionContext context) throws Exception {
    Method testMethod = context.getRequiredTestMethod();
    SlowTest annotation = testMethod.getAnnotation(SlowTest.class);
    Store store = getStore(context);
    Long start_time = store.remove("START_TIME", long.class);
    long duration = System.currentTimeMillis() - start_time;
    if (duration > THRESHOLD && annotation == null) {
      log.info("Please consider mark method {} with @SlowTest.", testMethod.getName());
    }
  }

  private Store getStore(ExtensionContext context) {
    String testClassName = context.getRequiredTestClass().getName();
    String testMethodName = context.getRequiredTestMethod().getName();
    return context.getStore(Namespace.create(testClassName, testMethodName));
  }
}

```

위의 확장 클래스는 테스트를 실행하기 전, 후의 시간을 비교하여 시간이 `THRESHOLD`를 넘으면서 `@SlowTest` 가 붙어있지 않으면 메세지를 출력하도록 하였습니다. 

실행코드
```java
@Slf4j
@ExtendWith(FindSlowTestExtension.class)
public class ExtensionTest {

  @FastTest
  void create() throws InterruptedException {
    Thread.sleep(1005L);
    log.info("create 실행");
  }

  @SlowTest
  void create1() {
    log.info("create1 실행");
  }
}

```
결과

![image](https://user-images.githubusercontent.com/63777714/153405828-b7cec7d2-1e36-4ca2-9d6f-68eb3a10ce08.png)

결과와 같이 느릴 경우 `@SlowTest` 어노테이션을 붙이라고 메세지가 출력되는 것을 볼 수 있습니다. 

자 그럼 두 번째 방법에 대해서 알아보겠습니다. 
위의 코드에서 `THRESHOLD`의 값을 변경하고 싶을 때 `THRESHOLD`를 변수로 선언해놓고 생성자로 주입받는 방법이 있습니다. 하지만 이는 `@ExtendWith`을 사용할 땐 생성자로 주입할 방법이 없습니다. 따라서 `@RegisterExtension`를 사용합니다. 

위의 코드에서 다음과 같이 변경하면 
```java
  private long THRESHOLD;

  public FindSlowTestExtension(Long THRESHOLD) {
    this.THRESHOLD = THRESHOLD;
  }
```
실행코드는 다음과 같이 작성할 수 있습니다. 
```java
@Slf4j
public class ExtensionTest {

  @RegisterExtension
  static FindSlowTestExtension findSlowTestExtension = new FindSlowTestExtension(1000L);

  @FastTest
  void create() throws InterruptedException {
    Thread.sleep(1005L);
    log.info("create 실행");
  }

  @SlowTest
  void create1() {
    log.info("create1 실행");
  }
}

```
