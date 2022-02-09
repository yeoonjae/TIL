# **ProxyFactory**

프록시 객체를 동적으로 생성하기 위해서 Java는 `JDK DynamicProxy`를 제공합니다. 이는 **인터페이스** 기반으로 만들어지기 때문에 인터페이스가 필수적입니다. **구체 클래스** 기반으로 프록시 객체를 만들고 싶을 땐 `CGLIB`를 사용합니다. 이는 **바이트** 코드를 기반으로 프록시를 생성하기 떄문에 인터페이스가 없어도 프록시 객체를 동적으로 생성할 수 있습니다. 

🤷‍♂️ 그렇다면 **인터페이스가 있는 경우엔 `JDK DynamicProxy`를 사용하고, 그렇지 않은 경우엔 `CGLIB`를 사용하려면 어떻게 해야할까요?**

스프링은 유사한 구체적인 기술들이 있을 때, 그것들을 통합해서 일관성있게 접근할 수 있고, 더욱 편리하게 사용할 수 있는 추상화된 기술을 제공합니다. 
 
스프링은 동적 프록시를 통합해서 편리하게 만들어주는 `ProxyFactory`라는 기술을 제공합니다. `ProxyFactory`는 인터페이스가 있으면 `JDK DynamicProxy`를 사용하고, 구체 클래스만 있다면 `CGLIB`를 사용합니다. 그리고 설정을 변경할 수도 있습니다. 


![image](https://user-images.githubusercontent.com/63777714/151906498-b0e5de24-0d7d-4f02-87da-6023823d9000.png)



 🤷‍♂️ **두 기술(JDK dynamicProxy, cglib)를 사용할 때 부가 기능을 적용하기 위해 JDK dynamicProxy의 `InvocationHandler`와 CGLIB의 `MethodInterceptor`를 각각 중복으로 따로 만들어야 하나요?**
 
스프링은 이 문제를 해결하기 위해 `Advice`라는 추상화된 새로운 개념을 도입했습니다. 부가 기능을 적용할 때 사용하며 `InvocationHandler`, `MethodInterceptor`을 신경쓰지 않고, `Advice`만 생성하면 됩니다. 

결과적으로 `InvocationHandler`, `MethodInterceptor`는 `Advice`를 호출하게 됩니다. ProxyFactory를 사용하면 `Advice`를 호출하는 전용 `InvocationHandler`,`MethodInterceptor`를 내부에서 사용합니다.

![image](https://user-images.githubusercontent.com/63777714/151907546-590f1667-a114-4c00-b96c-dbaee56045ad.png)

🤷‍♂️ **특정 조건에 맞을 때 프록시 로직을 적용하는 기능은요?**
스프링은 `Pointcut` 이라는 개념을 도입해 이 문제를 해결했습니다. 

---
## **예제 (Advice 만들기)**
---

### **Advice 만들기**
`Advice`는 프록시에 적용하는 부가 기능 로직입니다. 이것은 `InvocationHandler`, `MethodInterceptor`을 개념적으로 추상화 시킨 것으로 둘의 기능과 유사합니다. `ProxyFactory`를 사용하면 `InvocationHandler`, `MethodInterceptor` 대신에 `Advice`를 사용하면 됩니다. 

`Advice`를 만드는 기본적인 방법은 다음과 같습니다. 
```java
package org.aopalliance.intercept;

public interface MethodInterceptor extends Interceptor {
    Object invoke(MethodInvocation invocation) throws Throwable;
}
```
* `MethodInvocation invocation` : 내부에 다음 메소드를 호출하는 방법, 현재 프록시 객체의 인스턴스, args, 메소드 정보 등이 포함되어 있습니다. 
* `MethodInterceptor` 는 `Interceptor` 를 상속하고 `Interceptor` 는 `Advice` 인터페이스를 상속합니다. 
* `proceed`를 호출하면 됩니다. 

실행코드
```java
@Slf4j
public class ProxyFactoryTest {

  @Test
  @DisplayName("인터페이스가 있으면 JDK 동적 프록시 사용")
  void interfaceProxy() {
    ServiceInterface target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    proxyFactory.addAdvice(new TimeAdvice());
    ServiceInterface proxy = (ServiceInterface) proxyFactory.getProxy();
    log.info("targetClass={}", target.getClass());
    log.info("proxyClass={}", proxy.getClass());

    proxy.save();

    assertTrue(AopUtils.isAopProxy(proxy)); // A
    assertTrue(AopUtils.isJdkDynamicProxy(proxy));
  }
}
```
* `new ProxyFactory(target)` :  `ProxyFactory`를 생성할 때, 생성자에 프록시의 호출 대상을 함께
넘겨줍니다. `ProxyFactory`는 이 인스턴스 정보를 기반으로 프록시를 만들어낸다. 만약 이 인스턴스에
인터페이스가 있다면 JDK 동적 프록시를 기본으로 사용하고 인터페이스가 없고 구체 클래스만 있다면
CGLIB를 통해서 동적 프록시를 생성합니다. 
* `proxyFactory.addAdvice(new TimeAdvice())` : 프록시 팩토리를 통해서 만든 프록시가 사용할 부가
기능 로직을 설정합니다. `InvocationHandler` 와 `MethodInterceptor` 의 개념과 유사합니다. 
* `proxyFactory.getProxy()` : 프록시 객체를 생성하고 그 결과를 받습니다. 
* `AopUtils` 클래스를 사용하면 `ProxyFactory`가 잘 적용되었는지 확인이 가능합니다. 
* `proxyTargetClass` 를 true로 설정해놓으면 인터페이스가 있어도 cglib로 만들 수 있습니다. 

---
## **Pointcut, Advice, Advisor**
---
* `Pointcut` : 어디에 부가 기능을 적용할지, 안할지를 판단하는 필터링 로직. 주로 클래스와 메소드 이름으로 필터링. 이름 그대로 어떤 Point 에 기능을 적용할지 말지 잘라서(cut) 구분하는 것
* `Advice` : 프록시가 호출하는 부가 기능. 
* `Advisor` : 하나의 `Pointcut + Advice` . 

> 부가 기능 로직을 적용해야 하는데, <u>포인트컷으로 어디에?</u> 적용할지 선택하고, <u>어드바이스로 어떤
로직을 적용</u>할지 선택하는 것이다. 그리고 <u>어디에? 어떤 로직?을 모두 알고 있는 것이 어드바이저</u>

> 📚 쉽게 기억하기 <br>
조언( `Advice` )을 어디( `Pointcut` )에 할 것인가?
조언자( `Advisor` )는 어디( `Pointcut` )에 조언( `Advice` )을 해야할지 할지 알고 있다.

> 🔍 역할과 책임 <br>
> `Pointcut`은 대상 여부를 확인하는 필터 역할<br>
> `Advice` 는 깔끔하게 부가 기능 로직 담당 <br>
> `Advisor` 위의 둘을 합치면 Advisor가 된다. 

![image](https://user-images.githubusercontent.com/63777714/151912379-ee74a5c7-552d-4e22-a066-4c9ba3dcf9a4.png)

---
## **예제 (Advisor 만들기)**
---
```java
@Slf4j
public class AdvisorTest {

  @Test
  void advisorTest() {
    ServiceInterface target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(Pointcut.TRUE, new TimeAdvice());
    proxyFactory.addAdvisor(advisor);
    ServiceInterface proxy = (ServiceInterface) proxyFactory.getProxy();

    proxy.save();
    proxy.find();
  }
}

```
* `new DefaultPointcutAdvisor` : Advisor 인터페이스의 가장 일반적인 구현체. 생성자를 통해 하나의 포인트컷과 하나의 어드바이스를 넣어준다. 
* `Pointcut.TRUE` : 항상 true 를 반환하는 포인트컷
* `new TimeAdvice()` : 앞서 개발한 TimeAdvice 어드바이스를 제공
* `proxyFactory.addAdvisor(advisor)` : 프록시 팩토리에 적용할 어드바이저를 지정. 프록시 팩토리를 사용할 때 어드바이저는 필수.
* `proxyFactory.addAdvice(new TimeAdvice())`를 사용한다고 해도 내부적인 구현을 살펴보면 다음과 같이 어드바이저가 적용되어 있다. 
```java
DefaultPointcutAdvisor(Pointcut.TRUE, new TimeAdvice())
```

> 📚 스프링은 AOP 를 적용할 때 최적화를 진행해서 프록시를 하나만 만들고, 하나의 프록시에 여러 어드바이저를 적용한다. 즉, 하나의 `target`에 여러 AOP 가 동시에 적용되어도, 스프링의 AOP 는 `target`마다 하나의 프록시만 생성한다. 

---
## **예제 (Pointcut 만들기)**
---

Pointcut의 클래스는 다음과 같이 구현되어 있다. 
```java
public interface Pointcut {

	ClassFilter getClassFilter();

	MethodMatcher getMethodMatcher();

	Pointcut TRUE = TruePointcut.INSTANCE;

}

```
Pointcut은 `ClassFilter`와  `MethodMatcher`로 이루어져 있습니다. `ClassFilter`는 클래스의 정보가 맞는지, `MethodMatcher`는 메소드 정보가 맞는지 확인하며 둘 다 true를 반환해야 Advice를 적용할 수 있습니다. 

```java
@Test
  @DisplayName("직접 만든 포인트 컷")
  void advisorTest2() {
    ServiceInterface target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(new MyPointcut(), new TimeAdvice());
    proxyFactory.addAdvisor(advisor);
    ServiceInterface proxy = (ServiceInterface) proxyFactory.getProxy();

    proxy.save();
    proxy.find();
  }

  static class MyPointcut implements Pointcut{

    @Override
    public ClassFilter getClassFilter() {
      return ClassFilter.TRUE;
    }

    @Override
    public MethodMatcher getMethodMatcher() {
      return new MyMethodMatcher();
    }
  }

  static class MyMethodMatcher implements MethodMatcher{

    private String matchName =  "save";

    @Override
    public boolean matches(Method method, Class<?> targetClass) {
      boolean result = method.getName().equals(matchName);
      log.info("포인트컷 호출 method={} targetClass={}",method.getName(), targetClass);
      log.info("포인트컷 결과 result={}", result);
      return result;
    }

    @Override
    public boolean isRuntime() {
      return false;
    }

    @Override
    public boolean matches(Method method, Class<?> targetClass, Object... args) {
      throw new UnsupportedOperationException();
    }
  }
```

---
## **예제 (스프링이 제공하는 Pointcut 만들기)**
---
```java
@Test
  @DisplayName("스프링이 제공하는 포인트컷")
  void advisorTest3() {
    ServiceInterface target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    NameMatchMethodPointcut pointcut = new NameMatchMethodPointcut();
    pointcut.setMappedNames("save");
    DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(pointcut, new TimeAdvice());
    proxyFactory.addAdvisor(advisor);
    ServiceInterface proxy = (ServiceInterface) proxyFactory.getProxy();

    proxy.save();
    proxy.find();
  }
```

* `NameMatchMethodPointcut` : 메서드 이름을 기반으로 매칭. 내부에서는 PatternMatchUtils 를
사용. 예) *xxx* 허용
* `JdkRegexpMethodPointcut` : JDK 정규 표현식을 기반으로 포인트컷을 매칭.
* `TruePointcut` : 항상 참을 반환.
* `AnnotationMatchingPointcut` : 애노테이션으로 매칭.
* `AspectJExpressionPointcut` : aspectJ 표현식으로 매칭.

> **가장 중요한 것은 aspectJ 표현식** <br>
> 실무에서는 사용하기도 편리하고 기능도 가장 많은 aspectJ 표현식을 기반으로 사용하는 `AspectJExpressionPointcut` 을 사용하게 된다.

---
참고
- 스프링 핵심 원리-고급편 (인프런_김영한님)