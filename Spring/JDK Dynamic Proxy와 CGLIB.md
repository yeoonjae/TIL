# **JDK Dynamic Proxy와 CGLIB**


## 🔍 **JDK Dynamic Proxy와 CGLIB**

Spring AOP는 사용자의 특정 호출 시점에 IoC 컨테이너에 의해 AOP를 할 수 있는 Proxy Bean을 생성해준다.

동적으로 생성된 Proxy Bean은 타깃의 메소드가 호출되는 시점에 부가기능을 추가할 메소드를 자체적으로 판단하고 가로채어 부가기능을 주입해준다.

이처럼 호출 시점에 동적으로 위빙을 한다 하여 런타임 위빙(Runtime Weaving)이라 한다.

- Weaving
    : AOP에서 `Joinpoint들을 Advice로 감싸는 과정`을 `Weaving`이라고 한다.Weaving 하는 작업을 도와주는 것이 AOP 툴이 하는 역할
- Spring AOP는 런타임 위빙의 방식을 기반으로 하고 있다.
- Spring에선 런타임 위빙을 할 수 있도록 상황에 따라 `JDK Dynamic Proxy`와 `CGLIB` 방식을 통해 Proxy Bean을 생성을 해준다.

## 🤷‍♂️ **How? 구분**

- 프록시 대상 객체가 하나 이상의 **인터페이스를 구현하는 경우 JDK Dynamic Proxy가 사용**된다.
    - 대상 유형에 의해 구현된 모든 인터페이스가 프록시된다.
- 대상 개체가 **인터페이스를 구현하지 않으면 CGLIB 프록시**가 생성된다.
    
![image](https://user-images.githubusercontent.com/63777714/149284032-ccb2d2e7-3f2e-47d9-9b93-49c315b52924.png)

- **Why?**
    - JDK 프록시는 인터페이스 기반이며 인터페이스가 없다는 것은 JDK 프록시가 불가능하다는 것을 의미하기 때문이다.
- `ProxyFactoryBean`의 `proxyTargetClass`속성이 false로 설정된 경우에도 CGLIB 기반 프록시가 생성된다. (이외에 CGLIB으로 프록시 생성하는 설정이 더 있음)

---
## 🔍 **JDK Dynamic Proxy**

- JDK 다이내믹 프록시란 `java.lang.reflect.Proxy` 클래스를 사용함을 의미한다.
    - 리플렉션이란 클래스 자체의 원시적인 코드에 접근할 수 있도록 지원하는 Java API이다.
- 타깃의 인터페이스를 기준으로 Proxy를 생성해준다.

```java
Object proxy = Proxy.newProxyInstance(loader      // ClassLoader
                                     ,interfaces  // Class<?>[]
                                     ,handler     // InvocationHandler
                                  );
```

- JDK 다이내믹 프록시는 제공된 인터페이스의 정보를 통해 인터페이스의 구현체를 프록시 객체로 생성해주고
- 최종적으로 이 프록시 객체에 `InvocationHandler` 기능을 확장한 프록시 객체가 반환
- 필요한 부가기능은 `InvocationHandler`를 직접 구현하여 제공해줘야 한다.

```java
public interface InvocationHandler {
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable;
}
```
### **JDK Dynamic Proxy 동작원리**

![image](https://user-images.githubusercontent.com/63777714/149284190-881e2323-9971-4457-9568-38bce3103654.png)

1. 호출된 Method 정보를 리플렉션 정보로 변환하여 
2. `InvocationHandler.invoke()` 메소드에 제공해주고 `InvocationHandler`는 Method 정보를 통해 
3. 최종적으로 타깃의 메소드를 호출한다.

💡 **JDK Dynamic Proxy** 구현체는 인터페이스를 상속받아야하고, `@Autowired`를 통해 생성된 Proxy Bean을 사용하기 위해선 반드시 인터페이스의 타입으로 지정해줘야 한다. 다음의 코드에 JDK Dynamic Proxy 구현체를 생성하려 하면 에러가 발생한다. 

```java
@Controller
public class UserController{
  @Autowired
  private MemberService memberService; // <- Runtime Error 발생...
  ...
}

@Service
public class MemberService implements UserService{
  @Override
  public Map<String, Object> findUserId(Map<String, Object> params){
    ...isLogic
    return params;
  }
}
```
---
## 🔍 **CGLIB**

- CGLib은 Code Generator Library의 약자로, 클래스의 바이트코드를 조작하여 Proxy 객체를 생성해주는 라이브러리이다.
- **런타임에** Extends(상속) 방식을 이용해서 Proxy화 할 메서드를 오버라이딩 한다.
    - `Final` 메서드는 재정의할 수 없으므로 Advice할 수 없다. (단점)
- Spring은 CGLib을 사용하여 인터페이스가 아닌 타깃의 클래스에 대해서도 Proxy를 생성해주고 있다.
- CGLib은 Enhancer라는 클래스를 통해 Proxy를 생성할 수 있다.

```java
Enhancer enhancer = new Enhancer();
         enhancer.setSuperclass(MemberService.class); // 타깃 클래스
         enhancer.setCallback(MethodInterceptor);     // Handler
Object proxy = enhancer.create(); // Proxy 생성
```

```java
public interface MethodInterceptor extends Callback {
  Object intercept(Object var1, Method var2, Object[] var3, MethodProxy var4) throws Throwable;
}
```

> 💡 JDK Dynamic Proxy보다 CGLib의 MethodProxy이 예외를 발생시킬 가능성이 적다고 하여 Springboot에서는 `CGLIB`를 기본 프록시 객체 생성 라이브러리로 채택하게 되었다.
---
## **정리**


- JDK Dynamic Proxy는 인터페이스를 구현하여 Proxy를 생성해주고, Spring은 인터페이스가 아닌 클래스를 가지고 Proxy를 생성해주기 위해 CGLIB 방식을 지원하고 있다.
- CGLIB 클래스를 상속받아 Proxy를 생성해준다는 점과 JDK Dynamic Proxy보다 예외를 덜 발생시킨다는 점에서 Spring Boot에선 기본 Proxy 생성 방법으로 사용하고 있다.
- 또한, JDK Dynamic Proxy는 Spring AOP의 AOP 기술의 근간이 되는 방식이기 때문에 Spring에서 사용되는 AOP의 기술들은 Proxy 메커니즘을 따르고 있다. 즉 CGLib이든 JDK Dynamic Proxy든 Proxy 메커니즘을 따른다는 점을 인지하자.
- JDK Dynamic Proxy에서 InvocationHandler, CGLIB 에서 MethodInterceptor는 Spring AOP에서 JoinPoint 개념이다.
- 그리고 위에서 특정 조건에 의해 필터링 하는 MethodMatcher는 Spring AOP에서 PointCut 개념이다.
- 마지막으로 Proxy 로직이 실행되는 JDK Dynamic Proxy에 invoke 메서드, CGLIB 에서 Intercept 메서드는 Spring AOP에서 Advice 라는 개념이다.

> 💡 CGLIB 프록시와 동적 프록시 간의 성능 차이는 거의 없다.


---
참고한 사이트 <br>
[https://docs.spring.io/spring-framework/docs/4.0.1.RELEASE/spring-framework-reference/htmlsingle/#aop-understanding-aop-proxies](https://docs.spring.io/spring-framework/docs/4.0.1.RELEASE/spring-framework-reference/htmlsingle/#aop-understanding-aop-proxies)

[https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html](https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html)
