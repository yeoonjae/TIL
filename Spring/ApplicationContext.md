> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **ApplicationContext**
 > ApplicationContext는 최상위 게층인 BeanFactory를 상속받은 인터페이스로, BeanFactory의 기능들을 사용할 수 있으며 그 외의 다양한 기능들을 제공하는 인터페이스입니다.

 예제를 통해서 ApplicationContext를 사용하여 스프링 IoC 컨테이너를 몸소 느껴보도록 합시다. 

 1. `ApplicationContext` 에 `bean`을 직접 등록하기.
    * 해당 방법은 일일이 하나씩 bean으로 등록해야 하기때문에 번거롭다는 단점이 있습니다. 

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <!--
    bean을 설정할 때
     * id : 빈의 id 지정 , 소문자로 시작하는 것이 컨벤션임
     * class : 만들 bean의 타입
     * scope : 지정하지 않을 경우 기본적으로 singleton
     * autowire : byName, byType 등이 있으며, degault 값은 Type이다.

     하지만 해당 방법은 일일이 bean으로 등록을 해야한다는 단점이 있다.
  -->

  <bean id = "bookService"
    class="me.whiteship.springapplicationcontext.BookService">
    <!--property의 name은 setter에서 가져온 것이고, ref 는 다른 bean을 참조한다는 의미이다. 죽, 다른 bean의 id를 적어준다.-->
    <property name="bookRepository" ref="bookRepository"/>
  </bean>

  <bean id = "bookRepository"
    class="me.whiteship.springapplicationcontext.BookRepository">
  </bean>

</beans>
```

```java
// 실행코드
public class DemoApplication {

    public static void main(String[] args) {
        // application xml 파일을 불러온다.
        ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
        // 선언한 context에서 getBeanDefinitionNames() 메소드를 사용하면 context안의 bean id들을 불러올 수 있다.
        String[] beanDefinitionNames = context.getBeanDefinitionNames();
        System.out.println(Arrays.toString(beanDefinitionNames));

        // 결과 : [bookService, bookRepository]
    }

}
```

2. `component-scan` 을 이용하여 `bean` 등록하기.
    * 기본적으로 4가지의 어노테이션을 사용하여 bean으로 등록할 수 있습니다. 
        1. `@Compnent`
        2. `@Service` : `@Compnent`를 상속받는 어노테이션
        3. `@Repository` : `@Compnent`를 상속받는 어노테이션
        4. `@Controller`
    * 위의 어노테이션을 설정하면 bean으로는 등록이 되지만 의존성 주입은 되지 않습니다. 의존성 주입을 하려면 `@Autowired` 또는 `@Inject` 어노테이션을 사용해야 합니다. 
```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">


  <!--나는 이 package로부터 bean을 scan을 해서 등록을 하겠다. -->
  <context:component-scan base-package="me.whiteship.springapplicationcontext"/>

</beans>
```
```java
//Service 클래스
@Service
public class BookService {

    @Autowired
    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}

//Repository 클래스
@Repository
public class BookRepository {

}
```
실행코드는 1번과 동일하며 bean으로 등록하여 applicationContext를 실행했을 때와 결과는 동일하게 나옵니다. 

3. 자바파일로 설정파일 만들기.
    * 어노테이션 `@Configuration` 을 선언해 이 클래스는 설정파일 이라는 것을 명시할 수 있습니다. 
    * 어노테이션 `@ComponentScan` 을 선언해 xml 파일에서 `component-scan`을 사용했던 것 처럼 bean을 일일이 등록하지 않아도 bean으로 등록할 수 있습니다. 

```java
package me.whiteship.springapplicationcontext;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackageClasses = DemoApplication.class)
public class ApplicationConfig {

}
```
물론 해당 방법을 사용하려면 Service나 Repository로 사용할 클래스에 어노테이션을 설정해주어야 합니다. `@ComponentScan(basePackageClasses = DemoApplication.class)`에서 `basePackageClasses`가 의미하는 것은 scan할 클래스의 시작점을 의미합니다. 

현재 SpringBoot에서 사용하고 있는 설정방법이랑 가장 유사한 형태로 3번의 방법이 가장 간편합니다. 

----

## **📝 정리**
 **스프링 IoC 컨테이너의 역할**
  * 빈 인스턴스 생성
  * 의존 관계 설정
  * 빈 제공

**ApplicationContext** 
 * ClassPathXmlApplicationContext (XML)
 * AnnotationConfigApplicationContext (Java)

**Bean 설정** 
  * 이름 (id)
  * 클래스
  * 스코프
  * 생성자 아규먼트 (constructor)
  * 프로퍼트 (setter)

**컴포넌트 스캔**
 * 설정 방법
 * XML 설정에서는 context:component-scan
 * 자바 설정에서 @ComponentScan
 * 특정 패키지 이하의 모든 클래스 중에 @Component 애노테이션을 사용한 클래스를
빈으로 자동으로 등록

> ApplicationContext는 BeanFactory의 기능만 하는 것이 아니라, `ResourceLoader`, `ApplicationEventPublisher`, `MessageSource`, `EnvironmentCapable` ...  등 을 구현하고 있기에 BeanFactory 외에도 다양한 기능을 제공합니다. 