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

3. `WebMvcConfigurer` 인터페이스 : `@EnableWebMvc`가 제공하는 빈을 커스터마이징할 수 있는 기능을 제공하는 인터페이스
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
- `WebMvcConfigurationSupport.class`
- `WebMvcAutoConfiguration.class`
- 위의 두 클래스가 기본 설정입니다. 

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
    * @Configuration + Implements WebMvcConfigurer: 스프링 부트의 스프링 MVC
* 자동설정 + 추가 설정
    * @Configuration + @EnableWebMvc + Imlements WebMvcConfigurer: 스프링 부트의
스프링 MVC 자동설정 사용하지 않음