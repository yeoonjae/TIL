> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ์น MVC'๋ฅผ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>

# **์คํ๋ง MVC ์ค์ _1**

## **MVC ์ค์ **
1. ์คํ๋ง MVC ๊ตฌ์ฑ ์์ ์ง์  ๋น์ผ๋ก ๋ฑ๋กํ๊ธฐ
```java
@Configuration
@ComponentScan
public class WebConfig implements WebMvcConfigurer {

    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix("/WEB-INF/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}

```
2. `@EnableWebMvc` : ์ ๋ธํ์ด์ ๊ธฐ๋ฐ ์คํ๋ง MVC๋ฅผ ์ฌ์ฉํ  ๋ ํธ๋ฆฌํ ์น MVC ๊ธฐ๋ณธ ์ค์ ์ ํฉ๋๋ค. 
```java
@Configuration
@ComponentScan
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix("/WEB-INF/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}

```
```java
public class WebApplication implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.setServletContext(servletContext);
        context.register(WebConfig.class);
        context.refresh();

        DispatcherServlet dispatcherServlet = new DispatcherServlet(context);
        ServletRegistration.Dynamic app = servletContext.addServlet("app", dispatcherServlet);
        app.addMapping("/app/*");
    }
}
```
`@EnableWebMvc`๋ฅผ ์ฌ์ฉํ๊ธฐ ์ํด์๋ `WebApplicationInitializer`์ ๊ตฌํํ ํด๋์ค์์ servletContext๋ฅผ ๋ฐ๋์ ๋ฑ๋กํด์ฃผ์ด์ผ ํฉ๋๋ค. `@EnableWebMvc`๋ `DelegatingWebMvcConfiguration`์ ์์์ผ๋ฉฐ `DelegatingWebMvcConfiguration`๋ `WebMvcConfigurationSupport`๋ฅผ ์์๋ฐ๊ณ  ์์ต๋๋ค. `WebMvcConfigurationSupport` ๋ด๋ถ ๊ตฌํ์์ servletContext๋ฅผ ์ฐธ์กฐํ๊ธฐ ๋๋ฌธ์ servletContext๋ฅผ ๋ฑ๋กํด์ฃผ์ง ์์ผ๋ฉด ์ ๋๋ก ๋น ์ค์ ์ด ๋์ง ์์ต๋๋ค.  

> ๐ต๐ปโโ๏ธ `Delegation` : ์ด๋๊ฐ ์์ํด์ ์ฝ์ด์ค๋ ๊ตฌ์กฐ๋ฅผ ๋งํฉ๋๋ค. ํ์ฅ์ฑ์ ์ข๊ฒ ํ๊ธฐ ์ํด์ ์ฌ์ฉ๋ฉ๋๋ค. 

![image](https://user-images.githubusercontent.com/63777714/143907380-db7520f3-3639-4979-b12b-3ea4bcdc0e7d.png)

3. `WebMvcConfigurer` ์ธํฐํ์ด์ค : `@EnableWebMvc`๊ฐ ์ ๊ณตํ๋ ๋น์ ์ปค์คํฐ๋ง์ด์งํ  ์ ์๋ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ ์ธํฐํ์ด์ค์๋๋ค. ์ด๊ฒ์ ์ฌ์ฉํ๋ฉด MVC ๊ตฌ์ฑ์์์ ์ฌ์ฉ๋๋ Bean์ ํ๋์ฉ ๋ฑ๋กํ  ํ์ ์์ด ์ถ๊ฐ์ ์ธ ๊ฒ๋ง ํน์  ๋ฉ์๋๋ฅผ ๊ตฌํํจ์ผ๋ก์จ ํธํ๊ฒ ์ปค์คํฐ๋ง์ด์ง์ ํ  ์ ์์ต๋๋ค. 
```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/", ".jsp");
    }
}

```
---
## **์คํ๋ง ๋ถํธ MVC ์ค์ **
* ์คํ๋ง ๋ถํธ์ โ์ฃผ๊ดโ์ด ์ ์ฉ๋ ์๋ ์ค์ ์ด ๋์ํ๋ค.
    * JSP ๋ณด๋ค Thymeleaf ์ ํธ
    * JSON ์ง์
    * ์ ์  ๋ฆฌ์์ค ์ง์ (+ ์ฐ์ปด ํ์ด์ง, ํ๋น์ฝ ๋ฑ ์ง์)
* ์คํ๋ง MVC ์ปค์คํฐ๋ง์ด์ง
    * application.properties 
        - ์ฌ๊ธฐ์ ์คํ๋ง์ MVC์ค์  ๋ฑ์ ํ  ์ ์์ต๋๋ค.
        - ์๋ ์์ฑ์ด ๊ฐ๋ฅ : ์คํ๋ง ๋ถํธ๊ฐ ๋ฏธ๋ฆฌ ์ค์ ํ  ์ ์๊ฒ ๋ง๋ค์ด ๋์.
        - ๊ฐ์ฅ ์์ฝ๊ฒ ์ปค์คํฐ๋ง์ด์ง์ ํ  ์ ์๋ ๋ฐฉ๋ฒ (๊ฐ์ฅ ๋ณํ ์์ด..?)
    * `@Configuration` + `Implements WebMvcConfigurer`: ์คํ๋ง ๋ถํธ์ ์คํ๋ง MVC
* ์๋์ค์  + ์ถ๊ฐ ์ค์ 
    * `@Configuration` + `@EnableWebMvc` (+ `Imlements WebMvcConfigurer`): ์คํ๋ง ๋ถํธ์
์คํ๋ง MVC ์๋์ค์  ์ฌ์ฉํ์ง ์์

<br>

### ๊ธฐ๋ณธ์ค์ 
1.  `WebMvcAutoConfiguration.class` : Spring ์น MVC์ ์๋ ์ค์ ํ์ผ
    *  `ConditionalOnWebApplication` ์ application์ Type์ ์ค์ ํฉ๋๋ค. 

    > ๐ต๐ปโโ๏ธ Spring Boot application์ Type์ ์ด ์ธ๊ฐ์ง๊ฐ ์กด์ฌํฉ๋๋ค. <br>
    > * Servlet
    > * WebFlux
    > * Non-Web <br>

2. ๊ทธ ์ธ์๋ spring.factories ์ ๋ฑ๋ก๋ ์๋ ์ค์  ํ์ผ์ด ์กฐ๊ฑด์ ๋ฐ๋ผ ์ ์ฉ์ด ๋ฉ๋๋ค. 

![image](https://user-images.githubusercontent.com/63777714/143911482-29e20745-984e-453e-ba67-1df2e41d159a.png)


---
## **์คํ๋ง ๋ถํธ์์ JSP ์ฌ์ฉํ๊ธฐ**


* ์ ์ฝ์ฌํญ
    1. JAR ํ๋ก์ ํธ๋ก ๋ง๋ค ์ ์์, WAR ํ๋ก์ ํธ๋ก ๋ง๋ค์ด์ผ ํจ
    2. Java -JAR๋ก ์คํํ  ์๋ ์์ง๋ง โ์คํ๊ฐ๋ฅํ JAR ํ์ผโ์ ์ง์ํ์ง ์์
    3. ์ธ๋ํ ์ฐ(JBoss์์ ๋ง๋  ์๋ธ๋ฆฟ ์ปจํ์ด๋)๋ JSP๋ฅผ ์ง์ํ์ง ์์
    4. Whitelabel ์๋ฌ ํ์ด์ง๋ฅผ error.jsp๋ก ์ค๋ฒ๋ผ์ด๋ฉ ํ  ์ ์์.

    ![image](https://user-images.githubusercontent.com/63777714/143918447-b271730b-7188-4983-bf30-b3e93815e332.png)

---

## **Formatter ์ถ๊ฐํ๊ธฐ**

### **Formatter**
* `Printer` : ํด๋น ๊ฐ์ฒด๋ฅผ (Locale ์ ๋ณด๋ฅผ ์ฐธ๊ณ ํ์ฌ) ๋ฌธ์์ด๋ก ์ด๋ป๊ฒ ์ถ๋ ฅํ  ๊ฒ์ธ๊ฐ
* `Parser` : ์ด๋ค ๋ฌธ์์ด์ (Locale ์ ๋ณด๋ฅผ ์ฐธ๊ณ ํ์ฌ) ๊ฐ์ฒด๋ก ์ด๋ป๊ฒ ๋ณํํ  ๊ฒ์ธ๊ฐ 

### **Formatter ์ถ๊ฐํ๋ ๋ฐฉ๋ฒ**
* `WebMvcConfigurer`์ `addFormatters`(`FormatterRegistry`) ๋ฉ์๋ ์ ์
* ํด๋น ํฌ๋งคํฐ๋ฅผ ๋น์ผ๋ก ๋ฑ๋ก (์คํ๋ง ๋ถํธ์์๋ง ์ฌ์ฉ ๊ฐ๋ฅ)

```java
@RestController
public class SampleController {

    @GetMapping("/hello")
    public String hello(@RequestParam("name") Person person) {
        return "hello " + person.getName();
    }
}
```
```java
@Component
public class PersonFormatter implements Formatter<Person> {

    @Override
    public Person parse(String text, Locale locale) throws ParseException {
        Person person = new Person();
        person.setName(text);
        return person;
    }

    @Override
    public String print(Person object, Locale locale) {
        return object.toString();
    }
}

```
```java
public class Person {

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```
```java
@ExtendWith(SpringExtension.class)
@SpringBootTest
@AutoConfigureMockMvc
public class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;


    @Test
    public void hello() throws Exception {
        this.mockMvc.perform(get("/hello")
                .param("name", "yeonjae"))
            .andDo(print())
            .andExpect(content().string("hello yeonjae"));
    }


}
```

์ฐธ๊ณ  
* ์ด์  ์ ๋ฆฌ๊ธ : <a href = "https://github.com/yeoonjae/TIL/blob/main/Spring/DataBinder.md">DataBinder</a> 
* https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addFormatters-org.springframework.format.FormatterRegistry
* https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/format/Formatter.html

---
