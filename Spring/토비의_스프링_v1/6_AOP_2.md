> 📚 본 글은 토비의 스프링 3.1을 읽고 정리한 글입니다. 

<br>

# **6장 AOP_2**
# **스프링 AOP**
* 현재 타깃 오브젝트마다 거의 비슷한 내용의 ProxyFactoryBean Bean 설정 정보를 추가해주는 부분이 계속 중복되고 있다.
* ProxyFactoryBean의 장점?
    * 타깃 오브젝트로의 위임 코드와 부가 기능 적용을 위한 코드가 프록시가 구현해야 하는 모든 인터페이스 메소드마다 반복적으로 필요했다.
    * JDK의 다이내믹 프록시는 특정 인터페이스를 구현한 오브젝트에 대해서 프록시 역할을 해주는 클래스를 런타임시 내부적으로 만들어준다.
    * 변하지 않는 타깃으로의 위임과 부가기능 적용 여부 판단 부분은 코드 생성 기법을 이용하는 다이내믹 프록시 기술에 맡긴다.
    * 변하는 부가 기능 코드는 별도로 만들어서 다이내믹 프록시 생성 팩토리에 DI한다.

## **빈 후처리기(BeanPostProcessor)를 이용한 자동 프록시 생성기**
* 스프링은 컨테이너로서 제공하는 기능 중에서 변하지 않는 핵심적인 부분 외에는 대부분 확장할 수 있도록 확장 포인트를 제공해준다. 
    * 그 중 하나인 `BeanPostProcessor` 인터페이스를 구현해서 만드는 빈 후처리기는 말 그대로 **스프링 Bean 오브젝트로 만들어지고 난 후에, Bean 오브젝트를 다시 가공할 수 있게 해준다.** 

* `DefaultAdvisorAutiProxyCreator`는 스프링이 제공하는 Bean 후처리기 중의 하나이다.
* `DefaultAdvisorAutiProxyCreator` 는 어드바이저를 이용한 자동 프록시 생성기다. 
* Bean 후처리기가 스프링 Bean으로 등록하는 방법은 Bean 후처리가 자체를 빈으로 등록하는 방법을 사용한다. 
* 스프링은 Bean 후처리기가 Bean으로 등록되어 있으면 Bean 오브젝트가 생성될 때마다 Bean 후처리기에 보내서 후처리 작업을 요청한다. 
    * 이를 통해 Bean 오브젝트의 일부를 프록시로 포장하고, 프록시를 Bean으로 대신 등록할 수 있다.
    * 빈 후처리기는 Bean 오브젝트의 프로퍼티를 강제로 수정할 수 있다.
    * 별도의 초기화 작업을 수행할 수 있다.
    * 만들어진 빈 오브젝트를 강제로 바꿔치기할 수 있다. 
    * 스프링이 설정을 참고해서 만든 오브젝트가 아닌 다른 오브젝트를 Bean으로 등록시키는 것도 가능하다.

위의 빈 후처리기의 특징을 잘 이용한다면 스프링이 생성하는 빈 오브젝트의 일부를 프록시로 포장하고, 프록시를 빈으로 대신 등록할 수도 있다.

## **확장된 Pointcut**
Pointcut의 인터페이스는 다음과 같이 구성되어있다.
```java
public interface Pointcut {
    ClassFilter getClassFilter(); //프록시를 적용할 클래스인지 확인
    MethodMatcher getMethodMatcher(); //어드바이스를 적용할 메소드인지 확인
}
```
* Pointcut 인터페이스는 어드바이스를 적용할 메서드인지 확인하는 기능(`MethodMatcher`)뿐만 아니라 프록시를 적용할 수 있는 클래스인지 확인하는 기능(`ClassFilter`)을 포함한다.
    * DefaultAdvisorAutoProxyCreator는 Advisor 인터페이스를 구현한 Bean들을 전부 찾는다.
    * 등록된 어드바이저들의 포인트컷을 보고 전달받은 Bean이 프록시 적용 대상인지 확인한후, 맞다면 프록시를 생성하고 어드바이저와 연결해준다.
    * 최종적으로 타깃 오브젝트를 감싼 프록시 오브젝트로 바꿔치기 하여 Bean 컨테이너에게 반환한다.

포인트컷은 ClassFilter, MethodMatcher를 돌려주는 메소드를 갖고 있다. 이제까지는 메소드 선정 기능만 필요하였지만 DefaultAdvisorAutoProxyCreator는 클래스와 메소드 선정 알고리즘 모두가 필요하다. 두 가지 모두 충족해야 어드바이스가 적용된다.

```java
// HelloTest.java
@Test
void classNamePointcutAdvisor() {
    NameMatchMethodPointcut nameMatchMethodPointcut = new NameMatchMethodPointcut() {
        @Override
        public ClassFilter getClassFilter() {
            return new ClassFilter() {
                @Override
                public boolean matches(Class<?> clazz) {
                    // 클래스 이름이 HelloT로 시작하는 것만 선정한다. 
                    return clazz.getSimpleName().startsWith("HelloT");  
                }
            };
        }
    };
    // 메소드 이름이 sayH로 시작하는 메소드만 선정한다. 
    nameMatchMethodPointcut.setMappedName("sayH*");

    checkAdviced(new HelloTarget(), nameMatchMethodPointcut, true);
    checkAdviced(new HelloWorld(), nameMatchMethodPointcut, false);
    checkAdviced(new HelloToby(), nameMatchMethodPointcut, true);

}

private void checkAdviced(Hello hello, NameMatchMethodPointcut nameMatchMethodPointcut, boolean isAdviced) {
    ProxyFactoryBean proxyFactoryBean = new ProxyFactoryBean();
    proxyFactoryBean.setTarget(hello);
    proxyFactoryBean.addAdvisor(new DefaultPointcutAdvisor(nameMatchMethodPointcut, new UppercaseAdvice()));
    Hello proxy = (Hello) proxyFactoryBean.getObject();

    if (isAdviced) {
        assertThat(proxy.sayHello("abc")).isEqualTo("HELLOABC");
        assertThat(proxy.sayHi("abc")).isEqualTo("HIABC");
        assertThat(proxy.sayThankYou("abc")).isNotEqualTo("THANK YOUABC");
    } else {
        assertThat(proxy.sayHello("abc")).isNotEqualTo("HELLOABC");
        assertThat(proxy.sayHi("abc")).isNotEqualTo("HIABC");
        assertThat(proxy.sayThankYou("abc")).isNotEqualTo("THANK YOUABC");
    }
}
```
```java
// NameMatchMethodPointcut.java
public class NameMatchClassMethodPointcut extends NameMatchMethodPointcut {

    public void setMappedClassName(String mappedClassName) {
        this.setClassFilter(new SimpleClassFilter(mappedClassName));
    }

    static class SimpleClassFilter implements ClassFilter {

        String mappedName;

        private SimpleClassFilter(String mappedName) {
            this.mappedName = mappedName;
        }

        @Override
        public boolean matches(Class<?> clazz) {
            return PatternMatchUtils.simpleMatch(mappedName, clazz.getSimpleName());
        }
    }
}
```
```xml
<!-- applicationContext.xml -->
<bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator"/>
<bean id="userService" class="springbook.service.UserServiceImpl">
    <property name="userDao" ref="userDao"/>
    <property name="userLevelUpgradePolicy" ref="userLevelUpgradePolicy"/>
    <property name="mailSender" ref="mailSender"/>
</bean>
<bean id="transactionAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
    <property name="advice" ref="transactionAdvice"/>
    <property name="pointcut" ref="transactionPointcut"/>
</bean>
<bean id="transactionAdvice" class="springbook.proxy.TransactionAdvice">
    <property name="transactionManager" ref="transactionManager"/>
</bean>
<bean id="transactionPointcut" class="springbook.proxy.NameMatchClassMethodPointcut">
    <property name="mappedName" value="upgrade*"/>
    <property name="mappedClassName" value="*ServiceImpl"/>
</bean>
<bean id="testUserService" class="springbook.service.UserServiceTest$TestUserServiceImpl" parent="userService"/>
```
```java
// UserServiceTest.java
@Test
@DirtiesContext
void upgradeAllOrNothing() throws Exception {
    userDao.deleteAll();
    users.forEach(user -> userDao.addUser(user));

    try {
        testUserService.upgradeLevels();
    } catch (IllegalArgumentException e) {
        System.out.println("hi");
    }

    checkLevel(users.get(1), false);
}

@Test
void advisorProxyCreator() {
    assertThat(testUserService).isInstanceOf(java.lang.reflect.Proxy.class);
}

static class TestUserServiceImpl extends UserServiceImpl {

    private String id = "madnite1";

    @Override
    protected void upgradeLevel(User user) {
        if (user.getId().equals(this.id)) {
            throw new IllegalArgumentException();
        }
        super.upgradeLevel(user);
    }
}
```
* 복잡하던 UserService Bean의 설정을 원상복구시킨다.
* 트랜잭션 롤백 확인 테스트를 위해 생성한 static 테스트 클래스를 Bean으로 등록한다.
    * $를 통해 static 멤버 클래스임을 표기하고, 부모 클래스를 설정함(<parent>)으로써 다른 Bean 설정 내용을 상속받는다.
* 후처리기를 통해 자동으로 *ServiceImpl 이름의 Bean을 트랜잭션 부가 기능이 담긴 프록시 객체로 바꿔치기하기 때문에, 별도의 ProxyFactoryBean 없이도 프록시가 정상 생성됨을 확인할 수 있다.
* testUserService Bean이 프록시 객체로 바꿔치기 되었다면 타입은 TestUserServiceImpl이 아니라 JDK의 Proxy 클래스가 된다.

<br>

## **Pointcut 표현식을 이용한 Pointcut**
* 스프링은 아주 간단하고 효과적인 방법으로 포인트컷의 클래스와 메소드를 선정하는 알고리즘을 작성할 수 있는 **Pointcut 표현식**을 제공한다. 
* 이를 적용하려면 AspectJExpressionPointcut 클래스를 사용하면 된다. 
    * 스프링이 사용하는 포인트컷 표현식은 AspectJ라는 프레임워크에서 제공하는 것을 가져와 일부 문법을 확장해서 사용하는 것이다. 

```java
execution([접근제한자 패턴] 타입패턴 [타입패턴.]이름패턴 (타입패턴 | "..", ...) [throws 예외 패턴])

```
하나씩 살펴보자 (위의 execution의 순서대로 설명)
1. 접근제한자 패턴 
    * public, protected, private 등이 올 수 있다. 
    * 포인트컷 표현식에서 생략가능하다.
2. 타입 패턴 
    * 리턴값의 타입을 나타내는 패턴
    * 필수항목 - 생략 불가능
    * `*` 을 사용하여 모든 타입 선택 가능
3. [타입 패턴.]
    * 생략가능
    * 패키지와 타입이름을 포함한 클래스의 타입패턴
    * 뒤에 나오는 메소드 이름패턴과 `.`으로 연결되기 때문에 작성할 때 구분해야함.
    * `..`을 사용하여 여러 개의 패키지 선택 가능
4. 이름 패턴 
    * 메소드 이름 패턴이다. 
    * 필수항목
    * 모든 메소드가 대상이면 * 
5. (타입패턴 | "..", ...) 
    * 메소드 파라미터의 타입 패턴.
    * 파라미터의 타입 패턴을 순서대로 넣을 수 있다. 
    * 와일드카드를 이용해 개수에 상관없는 패턴 생성도 가능
    * 필수항목
6. 예외 패턴
    * 예외 이름에 대한 패턴
    * 생략 가능
```java
@Test
public void methodSignaturePointcut() throws SecurityException, NoSuchMethodException {
    AspectJExpressionPointcut aspectJExpressionPointcut = new AspectJExpressionPointcut();
    aspectJExpressionPointcut.setExpression("execution(public int springbook.learningtest.PointcutTest$Target.minus(int, int) throws java.lang.RuntimeException)");

    // Target.minus()
    assertThat(aspectJExpressionPointcut.getClassFilter().matches(Target.class) &&
            aspectJExpressionPointcut.getMethodMatcher().matches(Target.class.getMethod("minus", int.class, int.class), null))
            .isTrue(); // 테스트 통과

    // Target.plus()
    assertThat(aspectJExpressionPointcut.getClassFilter().matches(Target.class) &&
            aspectJExpressionPointcut.getMethodMatcher().matches(Target.class.getMethod("plus", int.class, int.class), null))
            .isFalse(); // 메소드 매치에서 실패
    
    // Target.method()
    assertThat(aspectJExpressionPointcut.getClassFilter().matches(Bean.class) &&
            aspectJExpressionPointcut.getMethodMatcher().matches(Target.class.getMethod("minus", int.class, int.class), null))
            .isFalse(); // 클래스 필터에서 실패
}

interface TargetInterface {
    void hello();

    void hello(String a);

    int minus(int a, int b);

    int plus(int a, int b);
}

static class Target implements TargetInterface {

    public void hello() {
    }

    public void hello(String a) {
    }

    public int minus(int a, int b) throws RuntimeException {
        return 0;
    }

    public int plus(int a, int b) {
        return 0;
    }

    public void method() {
    }
}

static class Bean {

    public void method() throws RuntimeException {
    }
}
```
* 표현식에서 옵션항목을 생략하면 `execution(int minus(int,int))` 처럼 간단하면서 느슨한 포인트컷을 만들 수 있다. 
    * `execution(int minus(int,int))` : int 타입의 리턴값, minus라는 메소드 이름, 두 개의 int 파라미터를 가진 모든 메소드를 선정하는 포인트 컷 표현식
* 리턴 값의 타입 제한을 없애려면 와일드카드를 쓰면 된다.
    * `execution(* minus(int, int))` : 리턴 타입은 상관없이 minus라는 메소드 이름, 두 개의 int 파라미터를 가진 모든 메소드를 선정하는 포인트컷 표현식
    * `execution(* minus(..))` : 리턴 타입과 파라미터의 종류, 개수에 상관없이 miuns라는 메소드 이름을 가진 모든 메소드를 선정하는 포인트컷 표현식
* 모든 선정조건을 다 없애고 모든 메소드를 다 허용하는 포인트컷은 다음과 같이 느슨하게 만들 수 있다. 
    * `execution(* *(..))` : 모든 메소드를 허용하는 포인트컷으로 가장 느슨하다. 
* 포인트컷 표현식을 이용하는 포인트컷 적용도 가능하다. 
    * `bean(* Service)` : 아이디가 Servicec로 끝나는 모든 Bean을 포인트컷 대상으로 한다. 
    * `@annotation(org.springframework.transaction.annotation.Transactional)` : 특정 애너테이션이 붙은 메서드를 포인트컷 대상으로 선정한다.
* 표현식의 `Target`을 `TargetInterface`로 변경해도 포인트컷이 잘 적용되며 해당 테스트는 통과한다.
    * 포인트컷 표현식의 클래스 이름에 적용되는 패턴은 클래스 이름 패턴이 아니라 타입 패턴이다.
    * `execution(* *..*Service.upgrade*(..))` 표현식은 UserService 인터페이스를 구현한 하위 타입 클래스들도 포인트컷이 적용된다.
```xml
<!-- applicationContext.xml -->
<aop:config>
    <aop:pointcut id="transactionPointcut" expression="execution(* *..*ServiceImpl.upgrade*(..))"/>
    <aop:advisor advice-ref="transactionAdvice" pointcut-ref="transactionPointcut"/>
</aop:config>
```
* 별도의 AOP 네임 스페이스를 이용해서 포인트컷과 어드바이저를 정의할 수 있다.
* 포인트컷 표현식의 클래스 이름에 적용되는 패턴은 클래스 이름 패턴이 아니라 타입 패턴으로 적용되기 때문에 TestUserService 의 타입은 TestUserService, 슈퍼 클래스인 UserServiceImpl, UserService 모두 적용 가능하다. 
---
## **AOP 란 무엇인가?**
비즈니스 로직을 담은 Userservice 에 트랜잭션을 적용해온 과정을 정리해보자. 
### **1. 트랜잭션 서비스 추상화**
* 문제 : 특정 트랜잭션 기술에 종속되는 코드
* 해결 : 트랜잭션 서비스 추상화 기법 적용
    * 추상적인 작업 내용은 유지한 채로 구체적인 방법을 자유롭게 바꿀 수 있도록
* 효과 : 방법과 서버환경에 종속되지 않음
* 구체적인 구현 내용을 담은 의존 오브젝트는 런타임 시에 다이내믹하게 연결해준다는 DI를 활용한 전형적인 접근 방법이다. 

### **2. 프록시와 데코레이터 패턴**
* 문제 : 비즈니스 로직 코드에 어울리지 않는 트랜잭션 코드가 적용되어 있음, 추상화로 인해 메소드 추출 방법으로 제거할 수 없음
* 해결 : DI를 이용해 데코레이터 패턴을 적용
* 효과 
    * 데코레이터 패턴을 적용하여 비즈니스 로직을 담당하는 클래스도 자신을 사용하는 클라이언트와 DI관계를 맺을 이유를 찾게 되었다. 
    * 클라이언트가 인터페이스와 DI를 통해 접근하도록 설계, 데코레이터 패턴을 통해 Service 클래스 코드에는 전혀 영향을 주지 않으며 트랜잭션이라는 부가기능을 자유롭게 부여할 수 있도록 만듦
    * 비즈니스 로직코드는 트랜잭션과 같은 성격이 다른 코드들로부터 자유로워졌음.
    * 독립적으로 로직을 검증하는 고립된 단위 테스트 가능

### **3. 다이내믹 프록시와 프록시 팩토리 빈**
* 문제 
    * 트랜잭션 기능을 부여하는 부여하는 코드를 넣는 프록시 클래스를 만드는 작업이 번거로움
    * 트랜잭션 기능을 부여하지 않아도 메소드를 모두 구현해야 함
* 해결 : 프록시 클래스 없이도 프록시 오브젝트를 런타임 시에 만들어주는 JDK **<u>다이내믹 프록시 기술을 적용</u>**
* 효과 
    * 프록시 클래스 만들지 않아도 됨 -> 번거로움 줄어듦
    * 중복되는 문제 일부 해결      
        * 문제 : 다만 동일한 기능을 여러 오브젝트에 적용할 경우 오브젝트 단위로 중복이 일어남
        * 해결 : JDK 다이내믹 프록시와 같은 프록시 기술을 추상화한 스프링의 **<u>프록시 팩토리 빈을 이용하여 다이내믹 프록시 생성 방법에 DI를 도입</u>**
        * 효과
            * 내부적으로 템플릿/콜백 패턴을 활용
            * 스프링의 프록시 팩토리 빈 덕분에 부가기능을 담은 어드바이스와 부가기능 선정 알고리즘을 담은 포인트 컷 프록시에서 분리됨
                * 여러 프록시에서 공유해서 사용할 수 있음.

### **4. 자동 프록시 생성 방법과 포인트 컷**
* 문제 : 트랜잭션 적용 대상이 되는 빈마다 일일이 프록시 팩토리 빈으로 설정해줘야 함. (번거로움)
* 해결 : 빈 생성 후처리 기법을 활용하여 초기화 시점에서 자동으로 프록시를 만들어주는 방법을 도입.
* 효과 
    * 패턴을 사용하여 프록시 적용 대상 자동 선정할 수 있도록 클래스 선정하는 기능을 담은 확장된 포인트컷 사용
    * 트랜잭션 부가기능을 어디에 적용하는지에 대한 정보를 포인트컷이라는 독립적인 정보로 완전히 분리
    * 포인트컷 표현식을 이용하여 깔끔하고 편리하게 적용 대상 쉽게 선택
---
## **AOP : 관점 지향 프로그래밍**
![image](https://user-images.githubusercontent.com/63777714/147542835-76b25e93-9794-4ed8-8e18-16e6a7bd79c3.png)

* 애플리케이션의 핵심적인 기능에서 분리한 부가기능을 분리하여 모듈화한 오브젝트를 에스펙트(Aspect)라고 부른다. 
* Aspect란 부가기능 모듈을 의미한다. 
    * 애플리케이션의 핵심 기능을 담고 있지는 않지만, 애플리케이션을 구성하는 중요한 한 가지 요소이고, 핵심기능에 부가되어 의미를 갖는 특별한 모듈을 말한다. 
    * 애플리케이션을 구성하는 한 가지 측면이라고 생각할 수 있다. 
    * Aspect는 Advice와 point cut을 함께 갖고있다. 지금까지 사용한 어드바이저는 단순한 형태의 Aspect라고 볼 수 있다. 
* 핵심기능은 순수하게 그 기능을 담은 코드로만 존재하고 독립적으로 살펴볼 수 있도록 분리한다. 
* OOP를 보완해주는 역할이다.

### **애플리케이션의 핵심적인 기능에서 부가적인 기능을 분리해서 Aspect라는 독특한 모듈로 만들어서 설계하고 개발하는 방법을 AOP(Aspect Oriented Programming) 관점 지향 프로그래밍이라고 한다.**

* AOP는 Aspect를 분리함으로써 핵심기능을 설계하고 구현할 때 객체지향적인 가치를 지킬 수 있도록 도와준다. 
* AOP는 애플리케이션을 다양한 측면에서 독립적으로 모델링, 설계, 개발할 수 있도록 만듦

---
## **AOP 적용기술**
### **Proxy 이용**
* 스프링 AOP 는 자바의 기본 JDK와 스프링 컨테이너 외에는 특별한 기술이나 환경을 요구하지 않는다. 
* 스프링 컨테이너인 애플리케이션 컨텍스트는 특별한 환경이나 JVM 설정 등을 요구하지 않는다. 
    * 서버환경이라면 기초적인 서블릿 컨테이너만으로 충분하다. 
* **스프링 AOP의 부가기능을 담은 `Advice`가 적용되는 대상은 오브젝트의 메소드이다.** 
* Advice가 구현한 MethodInterceptor 인터페이스는 프록시로부터 요청정보를 전달받아서 타깃 오브젝트의 메소드를 호출한다. 
    * 타깃의 메소드를 호출하는 전후에 다양한 부가기능 제공 가능

**독립적인 부가기능 모듈을 다양한 타깃 오브젝트의 메소드에 다이내믹하게 적용해주기 위해 가장 중요한 역할을 맡고 있는 게 Proxy이다. 따라서 스프링의 AOP는 Proxy 방식의 AOP라고 할 수 있다.**

### **바이트 코드 이용**
* 프록시 방식의 AOP외에도 바이트 코드를 이용한 AOP 방식이 있다. 
* AspectJ는 프록시처럼 간접적인 방법이 아닌, 타깃 오브젝트를 뜯어고쳐서 부가기능을 직접 넣어주는 직접적인 방식을 사용한다. 
* 컴파일된 타깃의 클래스 파일 자체를 수정하거나 클래스가 JVM에 로딩되는 시점을 가로채서 바이트코드를 조작하는 방법을 사용한다. 
    * 소스코드 수정을 하지 않는다.
* 왜 프록시 방식을 사용하지 않을까?
    1. DI컨테이너의 도움 없이, 자동 프록시 생성 방식을 사용하지 않아도 AOP를 적용할 수 있다. 
        * 스프링과 같은 컨테이너가 사용되지 않는 환경에서 손쉽게 AOP 적용이 가능하다.
    2. 프록시 방식보다 훨씬 강력하고 유연한 AOP 가 가능하다. 
        * 프록시 방식은 클라이언트가 호출할 때 부가기능의 대상은 메소드로 제한이 된다. 
        * 바이트코드 방식은 오브젝트 생성, 필드 값의 조회와 조작, 스태틱 초기화 등의 다양한 부가기능을 부여할 수 있다. 
    3. 사용하기 위해서는 JVM 실행 옵션을 변경하거나, 별도의 바이트 코드 컴파일러 혹은 클래스 로더를 사용해야 한다. 

---
## **AOP의 용어 정리**
### **타깃**
타깃은 부가기능을 부여할 대상이다. 

### **Advice(어드바이스)**
* 어드바이스는 타깃에게 제공할 부가기능을 담은 모듈이다. 
* 오브젝트로 정의하기도 하지만 메소드 레벨에서 정의할 수도 있다. 
* 어드바이스 종류 여러가지
    * MethodInterceptor 처럼 메소드 호출 과정 전반적으로 참여하는 어드바이스 존재
    * 예외가 발생했을 때만 동작하는 어드바이스처럼 메소드 호출 과정의 일부에서만 동작하는 어드바이스 존재

### **join point(조인 포인트)**
join point란 어드바이스가 적용될 수 있는 위치를 말한다. 스프링의 프록시 AOP에서 조인포인트는 메소드의 실행 단계뿐이다. 타깃 오브젝트가 구현한 인터페이스의 모든 메소드는 join point가 된다. 

### **point cut(포인트컷)**
* point cut이란 어드바이스를 적용할 join point를 선별하는 작업 또는 그 기능을 정의한 모듈을 말한다. 
* 스프링 AOP의 join point는 메소드의 실행뿐이므로 point cut 또한 메소드를 선정하는 기능을 갖고있다. 
    * 따라서 포인트 컷 표현식은 메소드의 실행이라는 의미의 execution으로 시작한다. 
    * 메소드는 클래스 안에 존재하는 것이기 때문에 메소드 선정이란 결국 클래스를 선정하고 그 안의 메소드를 선정하는 과정을 거치게 된다.

### **Proxy(프록시)**
* 프록시는 클라이언트와 타깃 사이에 투명하게 존재하면서 부가기능을 제공하는 오브젝트다. 
* DI를 통해 타깃 대신 클라이언트에게 주입되며, 크리아언트의 메소드 호출을 대신 받아서 타깃에 위임해주면서 그 과정에서 부가기능을 부여한다. 
* 스프링의 AOP는 프록시를 이용한다. 

### **Advisor(어드바이저)**
어드바이저는 point cut과 advice를 하나씩 갖고 있는 오브젝트이다. 
* 어떤 부가기능(Advice) 을 어디에(point cut) 전달할 것인가를 알고 있는 AOP의 가장 기본이 되는 모듈이다. 
* 스프링은 자동 프록시 생성기가 어드바이저를 AOP 작업의 정보로 활용한다. 

### **Aspect(애스펙트)**
* OOP의 클래스와 마찬가지로 Aspect는 AOP의 기본 모듈이다. 
* 한 개 또는 그 이상의 point cut과 advice의 조합으로 만들어지며 보통 싱글톤 형태의 오브젝트로 존재한다.
* 스프링의 Advisor는 단순한 형태의 Aspect로 볼 수 있다. 

---
## **AOP 네임스페이스**
스프링의 프록시 방식의 AOP를 적용하기 위해선 최소 네 가지 빈을 등록해야 한다. 
### **1. 자동 프록시 생성기**
스프링의 `DefaultAdvisorAutoProxyCreator` 클래스를 빈으로 등록한다. 
애플리케이션 컨텍스트가 빈 오브젝트를 생성하는 과정에 빈 후처리기로 참여한다. 빈으로 등록된 어드바이저를 이용해서 프록시를 자동으로 생성해주는 기능을 담당한다. 

### **2. Advice (어드바이스)**
부가기능을 구현한 클래스를 빈으로 등록한다. 

### **3. point cut (포인트컷)**
스프링의 AspectExpressionPointcut을 빈으로 등록하고 expression 프로퍼티에 포인트컷 표현식을 넣어준다. 

### **4. Advisor (어드바이저)**
스프링의 DefaultExpressionPointcut을 빈으로 등록해서 사용하게 된다. Advice와 point cut을 참조하는 것 외에는 기능이 없다. 자동 프록시 생성기에 의해 자동 검색되어 사용된다.  

### **AOP 네임스페이스**
스프링에서는 AOP와 관련된 태그를 정의해둔 aop 스키마를 제공한다. aop 스키마에 정의된 태그를 사용하려면 설정파일에 다음과 같이 추가해주어야 한다.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop" -- 이부분 추가
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
						http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
						http://www.springframework.org/schema/aop -- 추가
						http://www.springframework.org/schema/aop/spring-aop-3.0.xsd -- 추가>
```
위와 같이 네임스페이스를 추가했다면 AOP관련 빈 설정을 다음과 같이 바꿀 수 있다. 
```xml
<!-- aop --> 
<aop:config>
    <aop:pointcut id="transactionPointcut" expression="execution(* *..*ServiceImpl.upgrade*(..))*" />
    <aop:advisor advice-ref="transactionAdvice" pointcut-ref="transactionPointcut" />
</aop:config>
```
---
## **Transaction 속성**
* 트랜잭션은 더 이상 쪼갤 수 없는 최소 단위의 작업이라는 개념을 갖고 있다. 
* 트랜잭션 경계 안에서 진행된 작업은 `commit()` 또는 `rollback()`을 통해 모두 성공하던지 모두 취소되어야 한다. 
* 이 밖에도 트랜잭션의 동작 방식을 제어할 수 있는 몇가지 조건이 있다.
* `DefaultTransactionDefinition`이 구현하고 있는 `TransactionDefinition` 인터페이스는 트랜잭션의 동작방식에 영향을 줄 수 있는 네 가지 속성을 정의하고 있다. 

### 1. **트랜잭션 전파**
* 트랜잭션 전파란 트랜잭션 경계에서 이미 진행 중인 트랜잭션이 있거나 없을 때 동작할 것인가를 결정하는 방식을 말한다. 트랜잭션 전파 속성으로는 다음과 같이 정의할 수 있다. 
    * PROPAGATION_REQUIRED
        - 가장 많이 사용되는 전파 속성으로 진행중인 트랜잭션이 없으면 새로 시작하고 이미 시작된 트랜잭션이 있으면 이에 참여한다. 
        - `DefaultTransactionDefinition`의 트랜잭션 전파속성이 바로 PROPAGATION_REQUIRED 이다. 
    * PROPAGATION_REQUIRES_NEW
        - 항상 새로운 트랜잭션을 시작한다. 
        - 앞에서 시작된 트랜잭션이 있든 없은 상관없이 새로운 트랜잭션을 만들어서 독자적으로 동작하게 한다. 
    * PROPAGATION_NOT_SUPPORTED
        - 트랜잭션 없이 동작할 수 있도록 한다. 
        - 진행중인 트랜잭션이 있어도 무시한다. 
        - AOP를 이용해 한 번에 많은 메소드에 동시에 적용하는 방법을 사용한다. 

### 2. **격리수준**
* 모든 DB 트랜잭션은 격리수준을 갖고 있어야 한다. 서버환경에선 여러 개의 트랜잭션이 동시에 진행될 수 있다. 
* 따라서 격리수준을 설정해 가능한 한 많은 트랜잭션을 동시에 진행시키면서 문제가 발생하지 않도록 제어가 필요하다. 
* 격리수준은 기본적으로 DB에 설정되어 있지만 JDBC드라이버나 DataSource 등에서 재설정할 수 있고, 필요에 따라 트랜잭션 단위로 격리수준을 설정할 수 있다. 
* `DefaultTransactionDefinition`에 설정된 격리수준은 ISOLATION_DEFAULT 이다. 
    * DataSource 에 설정되어 있는 디폴트 격리 수준을 그대로 따른다는 것이다. 

### 3. **제한 시간**
* 트랜잭션을 수행하는 제한시간을 설정할 수 있다. 
* `DefaultTransactionDefinition`의 기본 설정은 제한시간이 없는 것이다. 
* 제한시간은 PROPAGATION_REQUIRED, PROPAGATION_REQUIRES_NEW 와 함께 사용될 때만 의미가 있다. 

### 4. **읽기전용**
* 읽기전용으로 설정해두면 트랜잭션 내에서 데이터를 조작하는 시도를 막아줄 수 있다. 

---
## **트랜잭션 인터셉터와 트랜잭션 속성**
메소드별로 다른 트랜잭션 정의를 사용하기 위해선 어드바이스 기능을 확장해야 한다. 

### **TransactionInterceptor**
* TransactionInterceptor는 Spring이 제공하며, 트랜잭션 정의를 메소드 이름 패턴을 이용해서 다르게 지정할 수 있는 방법을 제공한다.
* `rollbackOn()` 속성을 통해 기본 원칙과 다른 예외 처리를 가능하게 한다.
    * 기본적으로 런타임 예외가 발생하면 트랜잭션은 롤백된다.
    * 반면 체크 예외는 일종의 비즈니스 로직에 따른 의미있는 리턴 방식으로 인식해서 커밋해버린다.
        * Spring은 복구 가능성이 있는 예외 상황은 체크 예외를 사용한다고 가정하기 때문이다.
    * `rollbackOn()`을 활용하면 특정 체크 예외의 경우는 트랜잭션을 롤백시키고, 특정 런타임 예외에 대해서는 트랜잭션을 커밋시킬 수도 있다. 

```xml
<bean id="transactionAdvice" class="org.springframework.transaction.interceptor.TransactionInterceptor">
    <property name="transactionManager" ref="transactionManager"/>
    <property name="transactionAttributes">
        <props>
            <prop key="get*">PROPAGATION_REQUIRED,readOnly,timeout_30</prop>
            <prop key="upgrade*">PROPAGATION_REQUIRES_NEW,ISOLATION_SERIALIZABLE</prop>
            <prop key="*">PROPAGATION_REQUIRED</prop>
        </props>
    </property>
</bean>
```
* TransactionInterceptor는 메서드 이름 패턴에 부합하는 트랜잭션의 경우 사용할 특정한 전파 및 격리 옵션 등을 TransactionAttributes에 Key-Value로 설정한다.
    * 전파 옵션은 필수고 나머지는 선택 옵션이며, 생략하는 경우 DefaultTransactionDefinition의 속성을 따른다.
    * +나 -로 시작하는건 기본 원칙을 따르지 않는 예외를 설정하는 것이다.
* 패턴이 여러 개가 일치하는 경우 가장 구체적인 패턴을 적용한다.

### **tx 네임스페이스를 이용한 설정 방법**
* `TransactionInterceptor` 타입의 어드바이스 빈과 `TransactionAttribute` 타입의 속성 ㅈ어보도 tx 스키마의 전용 태그를 이용해 정의할 수 있다. 빈 설정파일을 다음과 같이 추가하자.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx" -- 추가
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
						http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
						http://www.springframework.org/schema/aop 
						http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
						http://www.springframework.org/schema/tx -- 추가
						http://www.springframework.org/schema/tx/spring-tx-3.0.xsd"> -- 추가

...생략
	
<tx:advice id="transactionAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="get*" propagation="REQUIRED" read-only="true" timeout="30"/>
        <tx:method name="upgrade*" propagation="REQUIRES_NEW" isolation="SERIALIZABLE"/>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
```
* 위처럼 Transaction 관련 네임 스페이스를 적용할 수 있다. 

```xml
<aop:config>
    <aop:advisor advice-ref="transactionAdvice" pointcut="bean(*Service)" />
    <aop:advisor advice-ref="batchTxAdvice" pointcut="execution(a.b.*BatchJob.*.(..))" />
</aop:config>
<tx:advice id="transactionAdvice">
  <tx:attributes>
      <tx:method name="get*" propagation="REQUIRED" read-only="true" timeout="30"/>
      <tx:method name="*" propagation="REQUIRED"/>
  </tx:attributes>
</tx:attributes>
<tx:advice id="batchTxAdvice">
    <tx:attributes>...</tx:attributes>
</tx:attributes>
```
* 위처럼 트랜잭션 포인트컷 표현식은 최소한의 타입 패턴이나 Bean 이름을 사용한다. 
    * 메소드나 파라미터, 예외에 대한 패턴을 정의하지 않는 것이 바람직하다. 
* 공통된 메소드 이름 규칙을 통해 최소한의 트랜잭션 어드바이스와 속성을 정의한다. 

![image](https://user-images.githubusercontent.com/63777714/147552681-ecb82d02-9f04-4f90-8813-d7b06cab82f9.png)

### **<u>프록시 방식의 AOP는 같은 타깃 오브젝트 내의 메소드를 호출하 때는 적용되지 않는다. </u>**
* 프록시 방식의 AOP에서는 프록시를 통한 부가기능의 적용은 클라이언트로부터 호출이 일어날 때만 가능하다. 
* 위 그림을 보면 [1]과 [3]처럼 클라이언트로부터 메소드가 호출되면 트랜잭션 프록시를 통해 타깃 메소드로 호출이 전달되므로 트랜잭션 경계설정 부가기능이 부여된다.
* 다만, [2] 와 같은 경우엔 타깃 오브젝트 내로 들어와서 타깃 오브젝트의 다른 메소드를 호출하는 경우에는 프록시를 거치지 않고 직접 타깃의 메소드가 호출되므로 AOP가 적용되지 않는다. 
* 같은 타깃 오브젝트 내의 메소드를 호출할 때는 AOP가 적용되지 않는다는 점을 인지하고 개발하자. 

--- 
## **트랜잭션 속성 적용**
* 일반적으로 특정 계층의 경계를 트랜잭션 경계와 일치시키는 것이 바람직하다. 비즈니스 로직을 담고 있는 서비스 계층 오브젝트의 메소드가 트랜잭션 경계를 부여하기에 가장 적절하다. 
    * 여러 계층에서 트랜잭션 경계 설정 부가 기능을 사용하는 것은 좋지 않다.
    * 가능하면 다른 모듈의 DAO에 접근할 때는 서비스 계층을 거치도록 하는 게 바람직하다. 

---
## **@Transactional**
트랜잭션 어노테이션의 클래스는 다음과 같이 정의되어 있다. 
```java
@Target({ElementType.TYPE, ElementType.METHOD}) // 어노테이션을 사용할 대상을 지정한다. 
@Retention(RetentionPolicy.RUNTIME) // 어노테이션 정보가 언제까지 유지되는지를 지정한다. 런타임 때도 어노테이션 정보를 리프렉션을 통해 얻을 수 있다. 
@Inherited // 상속을 통해서도 어노테이션 정보를 얻을 수 있게 한다. 
@Documented
public @interface Transactional {

	@AliasFor("transactionManager")
	String value() default "";

	@AliasFor("value")
	String transactionManager() default "";
	String[] label() default {};
	Propagation propagation() default Propagation.REQUIRED;
    ...

```
다음 그림은 `@Transactional` 어노테이션을 사용했을 때 어드바이저의 동작방식이다. 

![image](https://user-images.githubusercontent.com/63777714/147554696-de4790f3-ee85-414f-aa31-a7f20aa3b658.png)

* `@Transactional` 어노테이션의 타깃은 메소드와 타입이다. 
    * 따라서 메소드, 클래스 , 인터페이스에 사용할 수 있다. 
* 스프링은 `@Transactional`이 부여된 모든 오브젝트를 자동으로 타깃 오브젝트로 인식한다. 
    * 이때 사용되는 `TransactionAttributeSourcePointcut`은 스스로 표현식과 같은 선정기준을 갖고 있진 않다. 
    * `@Transactional`의 레벨에 상관없이 부여된 빈 오브젝트를 모두 찾아서 포인트컷의 선정 결과로 돌려준다. 
* `@Transactional` 은 기본적으로 트랜잭션 속성을 정의하는 것이지만, 동시에 포인터컷의 자동등록에도 사용된다. 
* `TransactionInterceptor`는 메소드 이름 패턴을 통해 부여되는 일괄적인 트랜잭션 속성정보 대신 `@Transactional` 어노테이션의 엘리먼트에서 트랜잭션 속성을 가져오는 `AnnotationTransactionAttributeSource`를 사용한다. 
* `@Transactional` 은 메소드마다 다르게 설정할 수도 있으므로 유연한 트랜잭션 속성 설정이 가능해진다. 
* 동시에 포인트컷도 `@Trnasactional` 을 통한 트랜잭션 속성정보를 참조하도록 만든다. 
* `@Transactional`로 트랜잭션 속성이 부여된 오브젝트라면 포인트컷의 선정 대상이기도 하기 때문이다. 
* 항상 메서드에 부여된 `@Transactional`이 가장 우선이기 때문에 `@Transactional`이 붙은 메서드는 클래스 레벨의 속성을 무시하고 메서드 레벨의 속성을 사용한다.
    * 반면에 `@Transactional`을 붙이지 않은 메서드는 클래스 레벨에 부여된 공통 `@Transactional`을 따른다.
    * 타깃 클래스에도 `@Transactional`이 없으면 클래스의 인터페이스에도 똑같이 `@Transactional`을 메서드 레벨부터 탐색한다.
    * `@Transactional`이 없으면 트랜잭션 적용 대상이 아니라고 판단한다.

![image](https://user-images.githubusercontent.com/63777714/147554633-5364dc87-5ed4-4003-81a8-37ed84513c23.png)

* AOP를 이용해 코드 외부에서 트랜잭션의 기능을 부여해주고 속성을 지정할 수 있게 하는 방법을 `선언적 트랜잭션`이라고 한다. 
* 반대로 TransactionTemplate 이나 개별 데이터 기술의 트랝객션 API를 사용해 직접 코드 안에서 사용하는 방법은 `프로그램에 의한 트랜잭션`이라고 한다. 
* 스프링은 위 두 가지 방법을 모두 지원한다. 
* 특별한 경우가 아니라면 선언적 방식의 트랜잭션을 사용하는 것이 바람직하다.
    * EJB도 선언적 트랜잭션을 지원하지만 특정 트랜잭션 기술 및 환경에 종속적이다. 

---
## **테스트를 위한 트랜잭션 어노테이션**

### **@Transactional**
* 테스트에도 @`Transactional`을 적용할 수 있다. 테스트 클래스 또는 메소드에 `@Transactional`을 부여해주면 테스트 메소드에 트랜잭션 경계가 자동으로 설정된다. 
* 테스트 내에서 진행하는 모든 트랜잭션 관련 작업을 하나로 묶을 수 있다. 
* `@Transactional` 을 클래스 레벨에 부여할 수도 있다. 
    * 테스트 클래스 내의 모든 메소드에 트랜잭션이 적용된다. 

### **@Rollback**
* 롤백 여부를 지정하는 값을 갖고 있다. 기본값은 true로 지정되어있다. 
* 롤백을 하지않으려면 `@Rollback(false)` 로 해줘야 한다. 
```java
@Test
@Transactional
@Rollback(false)
public void transactionSync(){
    ...
}
```
위의 코드는 테스트 트랜잭션을 커밋시키도록 설정한 것이다. 

### **@TransactionConfiguration**
* `@Transactional`은 테스트 클래스에 넣어서 모든 테스트 메소드에 일괄 적용할 수 있지만 `@Rollback` 어노테이션은 메소드 레벨에만 적용할 수 있다. 
* `@TransactionConfiguration` 애노테이션을 이용하면 클래스 레벨에 Rollback 설정을 적용할 수 있다. 
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "/test-applicationContext.xml")
@Transactional
@TransactionConfiguration(defaultRollback=false) // 롤백설정
public class UserServiceTest(){
    @Test
    @Rollback // 메소드레벨에 디폴트 설정과 그 밖의 롤백 방법으로 재설정 가능
    public void transactionSync(){
        ...
    }
}
```

### **@NotTransactional 과 Propagation.NEVER**
* `@NotTransactional`을 테스트 메소드에 부여하면 클래스 레벨의 `@Transactional` 설정을 무시하고 트랜잭션을 시작하지 않을 채로 테스트를 진행한다. 
* 트랜잭션 테스트와 비 트랜잭션 테스트의 클래스를 구분해서 만들도록 권장된다. 
* `@NotTransactional` 대신 `@Transactional(propagation=Propagaion.NEVER)`을 사용해도 트랜잭션이 시작되지 않는다. 

---
## **정리**
* 트랜잭션 경계설정 코드를 분리해서 별도의 클래스로 만들고 비즈니스 로직 클래스와 동일한 인터페이스를 구현하면 DI의 확장 기능을 이용해 클라이언트의 변경 없이도 깔끔하게 분리된 트랜잭션 부가기능을 만들 수 있다. 
* 트랜잭션처럼 환경과 외부 리소승-ㅔ 영향을 받는 코드를 분리하면 비즈니스 로직에만 충실한 테스트를 만들 수 있다. 
* 목 오브젝트를 활용하면 의존관계 속에 있는 오브젝트도 손쉽게 고립된 테스트로 만들 수 있다. 
* DI를 이용한 트랜잭션의 분리는 데코레이터 패턴과 프록시 패턴으로 이해될 수 있다. 
* 번거로운 프록시 클래스 작성은 JDK의 다이내믹 프록시를 사용하면 간단하게 만들 수 있다. 
* 다이내믹 프록시는 스태틱 팩토리 메소드를 사용하기 때문에 빈으로 등록하기 번거롭다. 따라서 팩토리 빈으로 만들어야 한다. 스프링은 자동 프록시 생성 기술에 대한 추상화 서비스를 제공하는 프록시 팩토리 빈을 제공한다. 
* 프록시 팩토리 빈의 설정이 반복되는 문제를 해결하기 위해 자동 프록시 생성기와 포인트컷을 활용할 수 있다. 
    * 자동 프록시 생성기는 부가기능이 담긴 어드바이스를 제공하는 프록시를 스프링 컨테이너 초기화 시점에 자동으로 만들어준다. 
* 프인트 컷은 AspectJ 포인트컷 표현식을 사용해서 작성하면 편리하다. 
* AOP는 OOP만으로는 모듈화하기 힘든 부가기능을 효과적으로 모듈화하도록 도와주는 기술이다. 
* 스프링은 자주 사용되는 AOP설정과 트랜잭션 속성을 지정하는 데 사용할 수 있는 전용 태그를 제공한다. 
* AOP를 이용해 트랜잭션 속성을 지정하는 방법에는 포인트컷 표현식과 이름 패턴을 이용하는 방법 외에 타깃에 직접 부여하는 @Transactional 어노테이션을 사용하는 방법이 있다. 
* @Transactional을 이용한 트랜잭션 속성을 테스트에 적용하면 손쉽게 DB를 사용하는 코드의 테스트를 만들 수 있다. 


---
참고한 사이트
* 토비의 스프링 3.1
* https://xlffm3.github.io/spring%20&%20spring%20boot/toby-spring-chapter6/