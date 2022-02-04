# **BeanPostProcessor (빈 후처리기)**

`@Bean` 이나 `@ComponentSacn`으로 스프링 빈으로 등록하면, 스프링은 대상 객체를 생성하고 컨테이너 내부의 빈 저장소에 등록합니다. 그리고 이후에는 스프링 컨테이너에 등록된 빈을 조회하여 사용합니다. 

하지만, 스프링 빈으로 등록해야하는 객체가 부가적인 작업이 필요하다면 어떻게 해야할까요?
이미 빈으로 등록된 객체를 변경해야 하는 경우엔? 이럴 때를 대비히여 `BeanPostProcessor`가 등장하였습니다. 

`BeanPostProcessor`을 사용한 빈 등록 과정은 다음과 같습니다. 
1. **생성** : 스프링 빈 대상이 되는 객체를 생성한다. (`@Bean`, `@ComponentScan` 모두 포함)
2. **전달** : 생성된 객체를 빈 저장소에 등록하기 직전에 빈 후처리기에 전달한다. 
3. **후 처리 작업** : 빈 후처리기는 전달된 스프링 빈 객체를 조작하거나 다른 객체로 바꿔치기 할 수 있다. 
4. **등록** : 빈 후처리기는 빈을 반환한다. 전달된 빈을 그대로 반환하면 해당 빈이 등록되고, 바꿔치기 하면 다른 객체가 빈 저장소에 등록된다. 

![image](https://user-images.githubusercontent.com/63777714/152541310-3820fe5c-7734-4b3c-85a8-6651c01c5dd2.png)

---
## **예제**

스프링에서 제공해주는 `BeanPostProcessor` 는 다음과 같이 인터페이스로 구성되어 있습니다. 그리고 빈 후처리기를 사용하려면 BeanPostProcessor 인터페이스를 구현하고, 스프링 빈으로 등록하면 됩니다. 

```java
public interface BeanPostProcessor {
 Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException
 Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException
}
```
* `postProcessBeforeInitialization` : 객체 생성 이후에 `@PostConstruct` 같은 초기화가 발생하기
전에 호출되는 포스트 프로세서입니다. 
* `postProcessAfterInitialization` : 객체 생성 이후에 @PostConstruct 같은 초기화가 발생한 다음에 호출되는 포스트 프로세서입니다. 

우선 일반적으로 빈 등록하는 예제입니다. 
```java
public class BasicTest {

  @Test
  void basicConfig() {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(BasicConfig.class);

    // A는 빈으로 등록된다.
    A a = applicationContext.getBean("beanA", A.class);
    a.helloA();

    // B는 빈으로 등록되지 않는다.
    Assertions.assertThrows(NoSuchBeanDefinitionException.class, () -> applicationContext.getBean(B.class));
  }

  @Slf4j
  @Configuration
  static class BasicConfig {

    @Bean(name = "beanA")
    public A a() {
      return new A();
    }
  }

  @Slf4j
  static class A {

    public void helloA() {
      log.info("hello A");
    }
  }

  @Slf4j
  static class B {

    public void helloB() {
      log.info("hello B");
    }
  }
}
```

다음은 빈 후처리기를 등록한 후 A 객체가 빈으로 등록된다면 B 객체로 바꿔치기 해주는 예제입니다. 
```java
public class BeanPostProcessorTest {

  @Test
  void basicConfig() {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(
        BeanPostProcessorConfig.class);

    // beanA 이름으로 B 객체가 빈으로 등록된다.
    B b = applicationContext.getBean("beanA", B.class);
    b.helloB();

    // A는 빈으로 등록되지 않는다.
    Assertions.assertThrows(NoSuchBeanDefinitionException.class,
        () -> applicationContext.getBean(A.class));
  }

  @Slf4j
  @Configuration
  static class BeanPostProcessorConfig {

    @Bean(name = "beanA")
    public A a() {
      return new A();
    }

    @Bean
    public AToBPostProcessor helloPostProcessor() {
      return new AToBPostProcessor();
    }
  }

  @Slf4j
  static class A {

    public void helloA() {
      log.info("hello A");
    }
  }

  @Slf4j
  static class B {

    public void helloB() {
      log.info("hello B");
    }
  }

  @Slf4j
  static class AToBPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName)
        throws BeansException {
      log.info("beanName={} bean={}", beanName, bean);
      if (bean instanceof A) {
        return new B();
      }
      return bean;
    }
  }
}
```
![image](https://user-images.githubusercontent.com/63777714/152545122-73c53c13-ae64-413d-9615-996f50c2296b.png)

* 위의 A.class 를 스프링 컨테이너에서 조회했을 떄 에러가 발생하는 것을 보면 beanA라는 이름으로 B.class가 등록된 것을 볼 수 있습니다. 
* 따라서 빈 후처리기는 빈을 조작하고 변경할 수 있는 후킹 포인트입니다. 
* 이것은 빈 객체를 조작하거나 다른 객체로 바꿔버릴 수 있는 막강한 기능입니다. 
* 이를 이용하면 모든 **빈을 중간에 프록시 객체로 변경**할 수 있습니다. 

> 🔍 **`@PostConstruct`의 비밀** <br>
> `@PostConstruct`는 스프링 빈 생성 이후에 빈을 초기화 하는 역할을 합니다. 즉, 생성된 빈을 한 번 조작하는 것입니다. 따라서 빈을 조작하는 행위를 하는 적절한 빈 후처리기가 있으면 될 것 같습니다. 스프링은 CommonAnnotationBeanPostProcessor라는 빈 후처리기를 자동으로 등록하는데, 여기에서 `@PostConstruct`어노테이션이 붙은 메소드를 호출합니다. 따라서 스프링 스스로도 스프링 내부의 기능을 확장하기 위해 빈 후처리기를 사용합니다. 

---
## **빈 후처리기 정리**

빈 후처리기를 사용함으로써 얻을 수 있는 장점을 정리하자면 다음과 같습니다. 

**문제**
1. 너무 많은 설정
    * 빈에 프록시를 통해 부가기능을 적용하려면 관련 빈에 모두 설정을 해주었어야 했습니다. 즉, 애플리케이션에 스프링 빈이 100개가 있다면 100개의 프록시 설정 코드가 들어가야 합니다. 
2. 컴포넌트 스캔
    * 컴포넌트 스캔으로 이미 스프링 컨테이너에 실제 객체를 스프링 빈으로 등록을 해놓은 상태이기 때문에 프록시를 적용할 수 없습니다. 

**문제 해결**
* 빈 후처리기를 통해 프록시를 생성하는 부분을 하나로 집중할 수 있습니다. 또한, 컴포넌트 스캔처럼 스프링이 직접 대상을 빈으로 등록하는 경우에도 중간 등록 과정을 가로채 원본 대신 프록시를 스프링 빈으로 등록할 수 있습니다. 
* 따라서 애플리케이션의 수많은 스프링 빈이 추가되어도 프록시와 관련된 코드는 전혀 변경되지 않아도 됩니다. 또한 컴포넌트 스캔을 사용해도 모두 적용됩니다. 

> 🔍 **중요** <br>
> 스프링 AOP는 포인트컷을 사용해서 프록시 적용 대상 여부를 체크한다.
>
> 1. 프록시 적용 대상 여부를 체크해서 꼭 필요한 곳에만 프록시를 적용한다. (빈 후처리기 - 자동 프록시
생성)
> 2. 프록시의 어떤 메서드가 호출 되었을 때 어드바이스를 적용할 지 판단한다. (프록시 내부)

---
## **스프링이 제공하는 빈 후처리기**

스프링이 제공하는 빈 후처리기를 사용하기 위해선 다음과 같은 의존성을 추가해줍니다. 
```xml
implementation 'org.springframework.boot:spring-boot-starter-aop'
```
이를 추가하면 자동 프록시 생성기 `AnnotationAwareAspectJAutoProxyCreator`라는 빈 후처리기를 등록해줍니다. 말 그대로 자동으로 프록시를 생성해주는 빈 후처리기입니다. 
* 이 빈 후처리기는 스프링 빈으로 등록된 Advisor 들을 자동으로 찾아서 프록시가 필요한 곳에 자동으로
프록시를 적용해줍니다. 
* Advisor 안에는 Pointcut 과 Advice 가 이미 모두 포함되어 있습니다. 따라서 Advisor 만 알고 있으면 그 안에 있는 Pointcut 으로 어떤 스프링 빈에 프록시를 적용해야 할지 알 수 있습니다. 그리고 Advice 로 부가 기능을 적용합니다. 

### **자동 프록시 생성기의 작동 과정**

![image](https://user-images.githubusercontent.com/63777714/152549606-a18b5009-011a-488c-b98b-34981b0528c1.png)
1. **생성**: 스프링이 스프링 빈 대상이 되는 객체를 생성한다. ( @Bean , 컴포넌트 스캔 모두 포함)
2. **전달**: 생성된 객체를 빈 저장소에 등록하기 직전에 빈 후처리기에 전달한다.
3. **모든 Advisor 빈 조회**: 자동 프록시 생성기 - 빈 후처리기는 스프링 컨테이너에서 모든 Advisor 를 조회한다.
4. **프록시 적용 대상 체크**: 앞서 조회한 Advisor 에 포함되어 있는 포인트컷을 사용해서 해당 객체가 프록시를 적용할 대상인지 아닌지 판단한다. 이때 객체의 클래스 정보는 물론이고, 해당 객체의 모든 메서드를 포인트컷에 하나하나 모두 매칭해본다. 그래서 조건이 하나라도 만족하면 프록시 적용 대상이 된다. 예를 들어서 10개의 메서드 중에 하나만 포인트컷 조건에 만족해도 프록시 적용 대상이 된다.
5. **프록시 생성**: 프록시 적용 대상이면 프록시를 생성하고 반환해서 프록시를 스프링 빈으로 등록한다. 만약 프록시 적용 대상이 아니라면 원본 객체를 반환해서 원본 객체를 스프링 빈으로 등록한다.
6. **빈 등록**: 반환된 객체는 스프링 빈으로 등록된다.