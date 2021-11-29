> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **스프링 MVC 설정**

## **MVC 설정**
1. 스프링 MVC 구성 요소 직접 빈으로 등록하기
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
2. `@EnableWebMvc` : 애노테이션 기반 스프링 MVC를 사용할 때 편리한 웹 MVC 기본 설정을 합니다. 
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
`@EnableWebMvc`를 사용하기 위해서는 `WebApplicationInitializer`을 구현한 클래스에서 servletContext를 반드시 등록해주어야 합니다. `@EnableWebMvc`는 `DelegatingWebMvcConfiguration`을 상속으며 `DelegatingWebMvcConfiguration`는 `WebMvcConfigurationSupport`를 상속받고 있습니다. `WebMvcConfigurationSupport` 내부 구현에서 servletContext를 참조하기 때문에 servletContext를 등록해주지 않으면 제대로 빈 설정이 되지 않습니다.  

> 🕵🏻‍♂️ `Delegation` : 어딘가 위임해서 읽어오는 구조를 말합니다. 확장성을 좋게 하기 위해서 사용됩니다. 

![image](https://user-images.githubusercontent.com/63777714/143907380-db7520f3-3639-4979-b12b-3ea4bcdc0e7d.png)

3. `WebMvcConfigurer` 인터페이스 : `@EnableWebMvc`가 제공하는 빈을 커스터마이징할 수 있는 기능을 제공하는 인터페이스입니다. 이것을 사용하면 MVC 구성요소에 사용되는 Bean을 하나씩 등록할 필요 없이 추가적인 것만 특정 메소드를 구현함으로써 편하게 커스터마이징을 할 수 있습니다. 
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
## **스프링 부트 MVC 설정**
* 스프링 부트의 “주관”이 적용된 자동 설정이 동작한다.
    * JSP 보다 Thymeleaf 선호
    * JSON 지원
    * 정적 리소스 지원 (+ 웰컴 페이지, 파비콘 등 지원)
* 스프링 MVC 커스터마이징
    * application.properties 
        - 여기서 스프링의 MVC설정 등을 할 수 있습니다.
        - 자동 완성이 가능 : 스프링 부트가 미리 설정할 수 있게 만들어 놓음.
        - 가장 손쉽게 커스터마이징을 할 수 있는 방법 (가장 변화 없이..?)
    * `@Configuration` + `Implements WebMvcConfigurer`: 스프링 부트의 스프링 MVC
* 자동설정 + 추가 설정
    * `@Configuration` + `@EnableWebMvc` (+ `Imlements WebMvcConfigurer`): 스프링 부트의
스프링 MVC 자동설정 사용하지 않음

<br>

### 기본설정
1.  `WebMvcAutoConfiguration.class` : Spring 웹 MVC의 자동 설정파일
    *  `ConditionalOnWebApplication` 은 application의 Type을 설정합니다. 

    > 🕵🏻‍♂️ Spring Boot application의 Type은 총 세가지가 존재합니다. <br>
    > * Servlet
    > * WebFlux
    > * Non-Web <br>

2. 그 외에도 spring.factories 에 등록된 자동 설정 파일이 조건에 따라 적용이 됩니다. 

![image](https://user-images.githubusercontent.com/63777714/143911482-29e20745-984e-453e-ba67-1df2e41d159a.png)


---
## **스프링 부트에서 JSP 사용하기**


* 제약사항
    1. JAR 프로젝트로 만들 수 없음, WAR 프로젝트로 만들어야 함
    2. Java -JAR로 실행할 수는 있지만 “실행가능한 JAR 파일”은 지원하지 않음
    3. 언더토우(JBoss에서 만든 서블릿 컨테이너)는 JSP를 지원하지 않음
    4. Whitelabel 에러 페이지를 error.jsp로 오버라이딩 할 수 없음.

    ![image](https://user-images.githubusercontent.com/63777714/143918447-b271730b-7188-4983-bf30-b3e93815e332.png)

---

## **Formatter 추가하기**

### **Formatter**
* `Printer` : 해당 객체를 (Locale 정보를 참고하여) 문자열로 어떻게 출력할 것인가
* `Parser` : 어떤 문자열을 (Locale 정보를 참고하여) 객체로 어떻게 변환할 것인가 

### **Formatter 추가하는 방법**
* `WebMvcConfigurer`의 `addFormatters`(`FormatterRegistry`) 메소드 정의
* 해당 포매터를 빈으로 등록 (스프링 부트에서만 사용 가능)

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

참고 
* 이전 정리글 : <a href = "https://github.com/yeoonjae/TIL/blob/main/Spring/DataBinder.md">DataBinder</a> 
* https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addFormatters-org.springframework.format.FormatterRegistry
* https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/format/Formatter.html

---
