# **JDK Dynamic Proxyμ CGLIB**


## π **JDK Dynamic Proxyμ CGLIB**

Spring AOPλ μ¬μ©μμ νΉμ  νΈμΆ μμ μ IoC μ»¨νμ΄λμ μν΄ AOPλ₯Ό ν  μ μλ Proxy Beanμ μμ±ν΄μ€λ€.

λμ μΌλ‘ μμ±λ Proxy Beanμ νκΉμ λ©μλκ° νΈμΆλλ μμ μ λΆκ°κΈ°λ₯μ μΆκ°ν  λ©μλλ₯Ό μμ²΄μ μΌλ‘ νλ¨νκ³  κ°λ‘μ±μ΄ λΆκ°κΈ°λ₯μ μ£Όμν΄μ€λ€.

μ΄μ²λΌ νΈμΆ μμ μ λμ μΌλ‘ μλΉμ νλ€ νμ¬ λ°νμ μλΉ(Runtime Weaving)μ΄λΌ νλ€.

- Weaving
    : AOPμμΒ `Joinpointλ€μ Adviceλ‘ κ°μΈλ κ³Όμ `μ `Weaving`μ΄λΌκ³  νλ€.Weaving νλ μμμ λμμ£Όλ κ²μ΄ AOP ν΄μ΄ νλ μ­ν 
- Spring AOPλ λ°νμ μλΉμ λ°©μμ κΈ°λ°μΌλ‘ νκ³  μλ€.
- Springμμ  λ°νμ μλΉμ ν  μ μλλ‘ μν©μ λ°λΌ `JDK Dynamic Proxy`μ `CGLIB` λ°©μμ ν΅ν΄ Proxy Beanμ μμ±μ ν΄μ€λ€.

## π€·ββοΈ **How? κ΅¬λΆ**

- νλ‘μ λμ κ°μ²΄κ° νλ μ΄μμ **μΈν°νμ΄μ€λ₯Ό κ΅¬ννλ κ²½μ° JDK Dynamic Proxyκ° μ¬μ©**λλ€.
    - λμ μ νμ μν΄ κ΅¬νλ λͺ¨λ  μΈν°νμ΄μ€κ° νλ‘μλλ€.
- λμ κ°μ²΄κ° **μΈν°νμ΄μ€λ₯Ό κ΅¬ννμ§ μμΌλ©΄ CGLIB νλ‘μ**κ° μμ±λλ€.
    
![image](https://user-images.githubusercontent.com/63777714/149284032-ccb2d2e7-3f2e-47d9-9b93-49c315b52924.png)

- **Why?**
    - JDK νλ‘μλ μΈν°νμ΄μ€ κΈ°λ°μ΄λ©° μΈν°νμ΄μ€κ° μλ€λ κ²μ JDK νλ‘μκ° λΆκ°λ₯νλ€λ κ²μ μλ―ΈνκΈ° λλ¬Έμ΄λ€.
- `ProxyFactoryBean`μ `proxyTargetClass`μμ±μ΄ falseλ‘ μ€μ λ κ²½μ°μλ CGLIB κΈ°λ° νλ‘μκ° μμ±λλ€. (μ΄μΈμ CGLIBμΌλ‘ νλ‘μ μμ±νλ μ€μ μ΄ λ μμ)

---
## π **JDK Dynamic Proxy**

- JDK λ€μ΄λ΄λ―Ή νλ‘μλΒ `java.lang.reflect.Proxy`Β ν΄λμ€λ₯Ό μ¬μ©ν¨μ μλ―Ένλ€.
    - λ¦¬νλ μμ΄λ ν΄λμ€ μμ²΄μ μμμ μΈ μ½λμ μ κ·Όν  μ μλλ‘ μ§μνλ Java APIμ΄λ€.
- νκΉμ μΈν°νμ΄μ€λ₯Ό κΈ°μ€μΌλ‘ Proxyλ₯Ό μμ±ν΄μ€λ€.

```java
Object proxy = Proxy.newProxyInstance(loader      // ClassLoader
                                     ,interfaces  // Class<?>[]
                                     ,handler     // InvocationHandler
                                  );
```

- JDK λ€μ΄λ΄λ―Ή νλ‘μλ μ κ³΅λ μΈν°νμ΄μ€μ μ λ³΄λ₯Ό ν΅ν΄ μΈν°νμ΄μ€μ κ΅¬νμ²΄λ₯Ό νλ‘μ κ°μ²΄λ‘ μμ±ν΄μ£Όκ³ 
- μ΅μ’μ μΌλ‘ μ΄ νλ‘μ κ°μ²΄μ `InvocationHandler` κΈ°λ₯μ νμ₯ν νλ‘μ κ°μ²΄κ° λ°ν
- νμν λΆκ°κΈ°λ₯μ `InvocationHandler`λ₯Ό μ§μ  κ΅¬ννμ¬ μ κ³΅ν΄μ€μΌ νλ€.

```java
public interface InvocationHandler {
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable;
}
```
### **JDK Dynamic Proxy λμμλ¦¬**

![image](https://user-images.githubusercontent.com/63777714/149284190-881e2323-9971-4457-9568-38bce3103654.png)

1. νΈμΆλ Method μ λ³΄λ₯Ό λ¦¬νλ μ μ λ³΄λ‘ λ³ννμ¬ 
2. `InvocationHandler.invoke()` λ©μλμ μ κ³΅ν΄μ£Όκ³  `InvocationHandler`λ Method μ λ³΄λ₯Ό ν΅ν΄ 
3. μ΅μ’μ μΌλ‘ νκΉμ λ©μλλ₯Ό νΈμΆνλ€.

π‘ **JDK Dynamic Proxy** κ΅¬νμ²΄λ μΈν°νμ΄μ€λ₯Ό μμλ°μμΌνκ³ , `@Autowired`λ₯Ό ν΅ν΄ μμ±λ Proxy Beanμ μ¬μ©νκΈ° μν΄μ  λ°λμ μΈν°νμ΄μ€μ νμμΌλ‘ μ§μ ν΄μ€μΌ νλ€. λ€μμ μ½λμ JDK Dynamic Proxy κ΅¬νμ²΄λ₯Ό μμ±νλ € νλ©΄ μλ¬κ° λ°μνλ€. 

```java
@Controller
public class UserController{
  @Autowired
  private MemberService memberService; // <- Runtime Error λ°μ...
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
## π **CGLIB**

- CGLibμ Code Generator Libraryμ μ½μλ‘, ν΄λμ€μ λ°μ΄νΈμ½λλ₯Ό μ‘°μνμ¬ Proxy κ°μ²΄λ₯Ό μμ±ν΄μ£Όλ λΌμ΄λΈλ¬λ¦¬μ΄λ€.
- **λ°νμμ** Extends(μμ) λ°©μμ μ΄μ©ν΄μ Proxyν ν  λ©μλλ₯Ό μ€λ²λΌμ΄λ© νλ€.
    - `Final`Β λ©μλλ μ¬μ μν  μ μμΌλ―λ‘ Adviceν  μ μλ€. (λ¨μ )
- Springμ CGLibμ μ¬μ©νμ¬ μΈν°νμ΄μ€κ° μλ νκΉμ ν΄λμ€μ λν΄μλ Proxyλ₯Ό μμ±ν΄μ£Όκ³  μλ€.
- CGLibμ EnhancerλΌλ ν΄λμ€λ₯Ό ν΅ν΄ Proxyλ₯Ό μμ±ν  μ μλ€.

```java
Enhancer enhancer = new Enhancer();
         enhancer.setSuperclass(MemberService.class); // νκΉ ν΄λμ€
         enhancer.setCallback(MethodInterceptor);     // Handler
Object proxy = enhancer.create(); // Proxy μμ±
```

```java
public interface MethodInterceptor extends Callback {
  Object intercept(Object var1, Method var2, Object[] var3, MethodProxy var4) throws Throwable;
}
```

> π‘ JDK Dynamic Proxyλ³΄λ€ CGLibμ MethodProxyμ΄ μμΈλ₯Ό λ°μμν¬ κ°λ₯μ±μ΄ μ λ€κ³  νμ¬ SpringbootμμλΒ `CGLIB`λ₯Ό κΈ°λ³Έ νλ‘μ κ°μ²΄ μμ± λΌμ΄λΈλ¬λ¦¬λ‘ μ±ννκ² λμλ€.
---
## **μ λ¦¬**


- JDK Dynamic Proxyλ μΈν°νμ΄μ€λ₯Ό κ΅¬ννμ¬ Proxyλ₯Ό μμ±ν΄μ£Όκ³ , Springμ μΈν°νμ΄μ€κ° μλ ν΄λμ€λ₯Ό κ°μ§κ³  Proxyλ₯Ό μμ±ν΄μ£ΌκΈ° μν΄ CGLIB λ°©μμ μ§μνκ³  μλ€.
- CGLIB ν΄λμ€λ₯Ό μμλ°μ Proxyλ₯Ό μμ±ν΄μ€λ€λ μ κ³Ό JDK Dynamic Proxyλ³΄λ€ μμΈλ₯Ό λ λ°μμν¨λ€λ μ μμ Spring Bootμμ  κΈ°λ³Έ Proxy μμ± λ°©λ²μΌλ‘ μ¬μ©νκ³  μλ€.
- λν, JDK Dynamic Proxyλ Spring AOPμ AOP κΈ°μ μ κ·Όκ°μ΄ λλ λ°©μμ΄κΈ° λλ¬Έμ Springμμ μ¬μ©λλ AOPμ κΈ°μ λ€μ Proxy λ©μ»€λμ¦μ λ°λ₯΄κ³  μλ€. μ¦ CGLibμ΄λ  JDK Dynamic Proxyλ  Proxy λ©μ»€λμ¦μ λ°λ₯Έλ€λ μ μ μΈμ§νμ.
- JDK Dynamic Proxyμμ InvocationHandler, CGLIB μμ MethodInterceptorλ Spring AOPμμ JoinPoint κ°λμ΄λ€.
- κ·Έλ¦¬κ³  μμμ νΉμ  μ‘°κ±΄μ μν΄ νν°λ§ νλ MethodMatcherλ Spring AOPμμ PointCut κ°λμ΄λ€.
- λ§μ§λ§μΌλ‘ Proxy λ‘μ§μ΄ μ€νλλ JDK Dynamic Proxyμ invoke λ©μλ, CGLIB μμ Intercept λ©μλλ Spring AOPμμ Advice λΌλ κ°λμ΄λ€.

> π‘ CGLIB νλ‘μμ λμ  νλ‘μ κ°μ μ±λ₯ μ°¨μ΄λ κ±°μ μλ€.


---
μ°Έκ³ ν μ¬μ΄νΈ <br>
[https://docs.spring.io/spring-framework/docs/4.0.1.RELEASE/spring-framework-reference/htmlsingle/#aop-understanding-aop-proxies](https://docs.spring.io/spring-framework/docs/4.0.1.RELEASE/spring-framework-reference/htmlsingle/#aop-understanding-aop-proxies)

[https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html](https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html)
