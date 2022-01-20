# **Reflection**

자바가 기본으로 제공하는 JDK 동적 프록시 기술이나, CGLIB 같은 프록시 생성 오픈소스 기술을 활용하면 프록시 객체를 동적으로 만들어낼 수 있습니다. 프록시 클래스를 계속 직접 만들지 않아도 되고, 프록시를 적용할 코드를 하나만 만들어두고, 동적 프록시 기술을 사용해서 프록시 객체를 찍어내는 것입니다. 

JDK 동적 프록시를 이해하기 위해선 Reflection의 개념을 이해해야 합니다. 

Reflection 기술을 사용하면 클래스나 메소드의 메타정보를 동적으로 획득하고, 코드도 동적으로 호출할 수 있습니다. 즉, 클래스와 메소드의 메타정보를 사용해서 애플리케이션을 동적으로 유연하게 만들 수 있습니다. 

다음과 같은 코드가 있습니다. 
```java
@Slf4j
public class ReflectionTest {

  @Test
  void reflection0() {
    Hello target = new Hello();

    // 공통 로직1 시작
    log.info("start");
    String result1 = target.callA();
    log.info("result={}",result1);
    // 공통 로직1 종료

    // 공통 로직2 시작
    log.info("start");
    String result2 = target.callB();
    log.info("result={}",result2);
    // 공통 로직2 종료

  }

  @Slf4j
  static class Hello {

    public String callA() {
      log.info("callA");
      return "A";
    }

    public String callB() {
      log.info("callB");
      return "B";
    }
  }

}
```

위의 코드 중에서 공통 로직 시작과 종료 부분은 중복됩니다. 
```java
og.info("start");
String result = xxx(); //호출 대상이 다름, 동적 처리 필요
log.info("result={}", result);
```
이를 공통적으로 사용할 수 있는 메소드로 뽑아서 사용하고 싶지만 호출하는 메소드가 다르기 때문에 생각보다 어렵다는 문제점이 있습니다. 이럴 때 클래스나 메소드의 메타정보를 동적으로 호출하는 메소드를 변경하게끔 Reflection 기술을 사용하여 봅시다.

```java
  @Test
  void reflection2() throws Exception {
    //클래스 정보
    Class classHello = Class.forName("hello.jdkdynamic.ReflectionTest$Hello");

    Hello target = new Hello();
    //callA 메서드 정보
    Method methodCallA = classHello.getMethod("callA");
    dynamicCall(methodCallA, target);

    //callB 메서드 정보
    Method methodCallB = classHello.getMethod("callB");
    dynamicCall(methodCallB, target);
  }

  private void dynamicCall(Method method, Object target) throws Exception{
    log.info("start");
    Object result = method.invoke(target);
    log.info("result={}", result);
  }
```
기존에 있었던 reflection0의 메소드를 reflection2로 변경하고 파라미터로 메소드와 target의 오브젝트를 받는 `dynamicCall()` 메소드를 만들었습니다. 

* `Class.forName()` : 클래스 메타정보를 획득
* `Class.getMethod("call")` : 해당 클래스의 `call`메소드 메타정보를 획득
* `methodCallA.invoke(target)` : 획득한 메소드의 메타정보로 실제 인스턴스의 메소드를 호출 (여기선 Hello 클래스의 call 이라는 메소드 호출)

이렇게 코드를 변경하면 클래스나 메소드의 정보를 동적으로 변경할 수 있습니다. 기존의 `callA()`, `callB()` 메소드를 직접 호출했던 부분을 Method 라는 추상적인 클래스로 대체되었고 , 이로인해 하나의 공통 메소드로 뺄 수 있습니다. 

* `dynamicCall(Method method, Object target)` 메소드
    * 공통로직1과 2를 모두 처리할 수 있는 통합된 공통 처리 로직
    * `Method` : 파라미터 넘어오는 메소드 정보. 호출할 메소드의 정보 동적으로 제공
    * `Object target` : 파라미터로 넘어오는 실제 실행할 인스턴스 정보. `method.invoke(target)` 사용시 호출할 클래스와 메소드 정보가 다를 경우 예외 발생

> 💡 정적인 `target.callA()` ,`target.callB()` 코드를 리플렉션을 사용해서 `Method` 라는 메타정보로
추상화했다. 덕분에 공통 로직을 만들 수 있게 되었다.

---
## **Reflection 주의** 
* Reflection은 런타임 시에 동작하기 때문에 컨파일 시점에 에러를 잡을 수 없습니다. 
* 그렇기에 개발자가 스스로 이를 인지하기 어렵습니다. (애플리케이션 서비스로 치면 배포하고 고객이 눌렀을 떄 예외 발생)
* 따라서 Reflection은 일반적으로 사용하면 안됩니다. 
* **Reflection은 프레임워크 개발이나 또는 매우 일반적인 공통 처리가 필요할 때 부분적으로 주의해서 사용**해야 합니다. 

> 가장 좋은 오류는 개발자가 즉시 확인할 수 있는 컴파일 오류이고, 가장 무서운 오류는 사용자가 직접 실행할 때 발생하는 런타임 오류라고 인프런 강의에서 김영한님이 말씀하십니다. ㅎㅎ

---
참고
- 스프링 핵심 원리-고급편 (인프런_김영한님)