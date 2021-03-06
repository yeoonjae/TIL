> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ํต์ฌ ๊ธฐ์ '์ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>

# **ApplicationContext**
 > ApplicationContext๋ ์ต์์ ๊ฒ์ธต์ธ BeanFactory๋ฅผ ์์๋ฐ์ ์ธํฐํ์ด์ค๋ก, BeanFactory์ ๊ธฐ๋ฅ๋ค์ ์ฌ์ฉํ  ์ ์์ผ๋ฉฐ ๊ทธ ์ธ์ ๋ค์ํ ๊ธฐ๋ฅ๋ค์ ์ ๊ณตํ๋ ์ธํฐํ์ด์ค์๋๋ค.

 ์์ ๋ฅผ ํตํด์ ApplicationContext๋ฅผ ์ฌ์ฉํ์ฌ ์คํ๋ง IoC ์ปจํ์ด๋๋ฅผ ๋ชธ์ ๋๊ปด๋ณด๋๋ก ํฉ์๋ค. 

 1. `ApplicationContext` ์ `bean`์ ์ง์  ๋ฑ๋กํ๊ธฐ.
    * ํด๋น ๋ฐฉ๋ฒ์ ์ผ์ผ์ด ํ๋์ฉ bean์ผ๋ก ๋ฑ๋กํด์ผ ํ๊ธฐ๋๋ฌธ์ ๋ฒ๊ฑฐ๋กญ๋ค๋ ๋จ์ ์ด ์์ต๋๋ค. 

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <!--
    bean์ ์ค์ ํ  ๋
     * id : ๋น์ id ์ง์  , ์๋ฌธ์๋ก ์์ํ๋ ๊ฒ์ด ์ปจ๋ฒค์์
     * class : ๋ง๋ค bean์ ํ์
     * scope : ์ง์ ํ์ง ์์ ๊ฒฝ์ฐ ๊ธฐ๋ณธ์ ์ผ๋ก singleton
     * autowire : byName, byType ๋ฑ์ด ์์ผ๋ฉฐ, degault ๊ฐ์ Type์ด๋ค.

     ํ์ง๋ง ํด๋น ๋ฐฉ๋ฒ์ ์ผ์ผ์ด bean์ผ๋ก ๋ฑ๋ก์ ํด์ผํ๋ค๋ ๋จ์ ์ด ์๋ค.
  -->

  <bean id = "bookService"
    class="me.whiteship.springapplicationcontext.BookService">
    <!--property์ name์ setter์์ ๊ฐ์ ธ์จ ๊ฒ์ด๊ณ , ref ๋ ๋ค๋ฅธ bean์ ์ฐธ์กฐํ๋ค๋ ์๋ฏธ์ด๋ค. ์ฃฝ, ๋ค๋ฅธ bean์ id๋ฅผ ์ ์ด์ค๋ค.-->
    <property name="bookRepository" ref="bookRepository"/>
  </bean>

  <bean id = "bookRepository"
    class="me.whiteship.springapplicationcontext.BookRepository">
  </bean>

</beans>
```

```java
// ์คํ์ฝ๋
public class DemoApplication {

    public static void main(String[] args) {
        // application xml ํ์ผ์ ๋ถ๋ฌ์จ๋ค.
        ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
        // ์ ์ธํ context์์ getBeanDefinitionNames() ๋ฉ์๋๋ฅผ ์ฌ์ฉํ๋ฉด context์์ bean id๋ค์ ๋ถ๋ฌ์ฌ ์ ์๋ค.
        String[] beanDefinitionNames = context.getBeanDefinitionNames();
        System.out.println(Arrays.toString(beanDefinitionNames));

        // ๊ฒฐ๊ณผ : [bookService, bookRepository]
    }

}
```

2. `component-scan` ์ ์ด์ฉํ์ฌ `bean` ๋ฑ๋กํ๊ธฐ.
    * ๊ธฐ๋ณธ์ ์ผ๋ก 4๊ฐ์ง์ ์ด๋ธํ์ด์์ ์ฌ์ฉํ์ฌ bean์ผ๋ก ๋ฑ๋กํ  ์ ์์ต๋๋ค. 
        1. `@Compnent`
        2. `@Service` : `@Compnent`๋ฅผ ์์๋ฐ๋ ์ด๋ธํ์ด์
        3. `@Repository` : `@Compnent`๋ฅผ ์์๋ฐ๋ ์ด๋ธํ์ด์
        4. `@Controller`
    * ์์ ์ด๋ธํ์ด์์ ์ค์ ํ๋ฉด bean์ผ๋ก๋ ๋ฑ๋ก์ด ๋์ง๋ง ์์กด์ฑ ์ฃผ์์ ๋์ง ์์ต๋๋ค. ์์กด์ฑ ์ฃผ์์ ํ๋ ค๋ฉด `@Autowired` ๋๋ `@Inject` ์ด๋ธํ์ด์์ ์ฌ์ฉํด์ผ ํฉ๋๋ค. 
```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">


  <!--๋๋ ์ด package๋ก๋ถํฐ bean์ scan์ ํด์ ๋ฑ๋ก์ ํ๊ฒ ๋ค. -->
  <context:component-scan base-package="me.whiteship.springapplicationcontext"/>

</beans>
```
```java
//Service ํด๋์ค
@Service
public class BookService {

    @Autowired
    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}

//Repository ํด๋์ค
@Repository
public class BookRepository {

}
```
์คํ์ฝ๋๋ 1๋ฒ๊ณผ ๋์ผํ๋ฉฐ bean์ผ๋ก ๋ฑ๋กํ์ฌ applicationContext๋ฅผ ์คํํ์ ๋์ ๊ฒฐ๊ณผ๋ ๋์ผํ๊ฒ ๋์ต๋๋ค. 

3. ์๋ฐํ์ผ๋ก ์ค์ ํ์ผ ๋ง๋ค๊ธฐ.
    * ์ด๋ธํ์ด์ `@Configuration` ์ ์ ์ธํด ์ด ํด๋์ค๋ ์ค์ ํ์ผ ์ด๋ผ๋ ๊ฒ์ ๋ช์ํ  ์ ์์ต๋๋ค. 
    * ์ด๋ธํ์ด์ `@ComponentScan` ์ ์ ์ธํด xml ํ์ผ์์ `component-scan`์ ์ฌ์ฉํ๋ ๊ฒ ์ฒ๋ผ bean์ ์ผ์ผ์ด ๋ฑ๋กํ์ง ์์๋ bean์ผ๋ก ๋ฑ๋กํ  ์ ์์ต๋๋ค. 

```java
package me.whiteship.springapplicationcontext;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackageClasses = DemoApplication.class)
public class ApplicationConfig {

}
```
๋ฌผ๋ก  ํด๋น ๋ฐฉ๋ฒ์ ์ฌ์ฉํ๋ ค๋ฉด Service๋ Repository๋ก ์ฌ์ฉํ  ํด๋์ค์ ์ด๋ธํ์ด์์ ์ค์ ํด์ฃผ์ด์ผ ํฉ๋๋ค. `@ComponentScan(basePackageClasses = DemoApplication.class)`์์ `basePackageClasses`๊ฐ ์๋ฏธํ๋ ๊ฒ์ scanํ  ํด๋์ค์ ์์์ ์ ์๋ฏธํฉ๋๋ค. 

ํ์ฌ SpringBoot์์ ์ฌ์ฉํ๊ณ  ์๋ ์ค์ ๋ฐฉ๋ฒ์ด๋ ๊ฐ์ฅ ์ ์ฌํ ํํ๋ก 3๋ฒ์ ๋ฐฉ๋ฒ์ด ๊ฐ์ฅ ๊ฐํธํฉ๋๋ค. 

----

## **๐ ์ ๋ฆฌ**
 **์คํ๋ง IoC ์ปจํ์ด๋์ ์ญํ **
  * ๋น ์ธ์คํด์ค ์์ฑ
  * ์์กด ๊ด๊ณ ์ค์ 
  * ๋น ์ ๊ณต

**ApplicationContext** 
 * ClassPathXmlApplicationContext (XML)
 * AnnotationConfigApplicationContext (Java)

**Bean ์ค์ ** 
  * ์ด๋ฆ (id)
  * ํด๋์ค
  * ์ค์ฝํ
  * ์์ฑ์ ์๊ท๋จผํธ (constructor)
  * ํ๋กํผํธ (setter)

**์ปดํฌ๋ํธ ์ค์บ**
 * ์ค์  ๋ฐฉ๋ฒ
 * XML ์ค์ ์์๋ context:component-scan
 * ์๋ฐ ์ค์ ์์ @ComponentScan
 * ํน์  ํจํค์ง ์ดํ์ ๋ชจ๋  ํด๋์ค ์ค์ @Component ์ ๋ธํ์ด์์ ์ฌ์ฉํ ํด๋์ค๋ฅผ
๋น์ผ๋ก ์๋์ผ๋ก ๋ฑ๋ก

> ApplicationContext๋ BeanFactory์ ๊ธฐ๋ฅ๋ง ํ๋ ๊ฒ์ด ์๋๋ผ, `ResourceLoader`, `ApplicationEventPublisher`, `MessageSource`, `EnvironmentCapable` ...  ๋ฑ ์ ๊ตฌํํ๊ณ  ์๊ธฐ์ BeanFactory ์ธ์๋ ๋ค์ํ ๊ธฐ๋ฅ์ ์ ๊ณตํฉ๋๋ค. 