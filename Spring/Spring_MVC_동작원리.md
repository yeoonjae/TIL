> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ์น MVC'๋ฅผ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>

# **์คํ๋ง MVC ๋์ ์๋ฆฌ**
# **Servlet**

* **์๋ธ๋ฆฟ ์๋ช ์ฃผ๊ธฐ**
    - ์๋ธ๋ฆฟ ์ปจํ์ด๋๊ฐ ์๋ธ๋ฆฟ ์ธ์คํด์ค์ `init()` ๋ฉ์๋๋ฅผ ํธ์ถํ์ฌ ์ด๊ธฐํ ํฉ๋๋ค. 
        - ์ต์ด ์์ฒญ์ ๋ฐ์์ ๋ ํ ๋ฒ ์ด๊ธฐํ ํ๊ณ  ๋๋ฉด ๊ทธ ๋ค์ ์์ฒญ๋ถํฐ๋ ์ด ๊ณผ์ ์ ์๋ตํฉ๋๋ค. 
    - ์๋ธ๋ฆฟ์ด ์ด๊ธฐํ ๋ ๋ค์๋ถํฐ ํด๋ผ์ด์ธํธ์ ์์ฒญ์ ์ฒ๋ฆฌํ  ์ ์์ต๋๋ค. ๊ฐ ์์ฒญ์ ๋ณ๋์ ์ฐ๋ ๋๋ก ์ฒ๋ฆฌํ๊ณ  ์ด ๋ ์๋ธ๋ฆฟ ์ธ์คํด์ค์ `service()` ๋ฉ์๋๋ฅผ ํธ์ถํฉ๋๋ค.
        - ์ด ์์์ HTTP ์์ฒญ์ ๋ฐ๊ณ , ํด๋ผ์ด์ธํธ๋ก ๋ณด๋ผ HTTP ์๋ต์ ๋ง๋ญ๋๋ค.
        - `service()` ๋ ๋ณดํต HTTP Method ์ ๋ฐ๋ผ `doGet()`, `doPost()` ๋ฑ์ผ๋ก ์ฒ๋ฆฌํฉ๋๋ค.
        - ๋ฐ๋ผ์ ๋ณดํต `doGet()` ๋๋ `doPost()` ๋ฅผ ๊ตฌํํฉ๋๋ค.
    - ์๋ธ๋ฆฟ ์ปจํ์ด๋ ํ๋จ์ ๋ฐ๋ผ ํด๋น ์๋ธ๋ฆฟ์๋ฉ๋ชจ๋ฆฌ์์ ๋ด๋ ค์ผ ํ  ์์ ์ `destroy()`๋ฅผ ํธ์ถํฉ๋๋ค.

---
# **Servlet Listener์ Servlet Filter**

## **Servlet Listener**
* ์น ์ ํ๋ฆฌ์ผ์ด์์์ ๋ฐ์ํ๋ ์ฃผ์ ์ด๋ฒคํธ๋ฅผ ๊ฐ์งํ๊ณ  ๊ฐ ์ด๋ฒคํธ์ ํน๋ณํ ์์์ด ํ์ํ ๊ฒฝ์ฐ์ ์ฌ์ฉํ  ์ ์์ต๋๋ค. 
    * Servlet Context ์์ค์ ์ด๋ฒคํธ
        * ์ปจํ์คํธ ๋ผ์ดํ์ฌ์ดํด ์ด๋ฒคํธ
        * ์ปจํ์คํธ ์ ํธ๋ฆฌ๋ทฐํธ ๋ณ๊ฒฝ ์ด๋ฒคํธ
    * ์ธ์ ์์ค์ ์ด๋ฒคํธ
        * ์ธ์ ๋ผ์ดํ์ฌ์ดํด ์ด๋ฒคํธ
        * ์ธ์ ์ ํธ๋ฆฌ๋ทฐํธ ๋ณ๊ฒฝ ์ด๋ฒคํธ

## **Servlet Filter**
* ๋ค์ด์จ ์์ฒญ์ ์๋ธ๋ฆฟ์ผ๋ก ๋ณด๋ด๊ณ , ๋ ์๋ธ๋ฆฟ์ด ์์ฑํ ์๋ต์ ํด๋ผ์ด์ธํธ๋ก ๋ณด๋ด๊ธฐ ์ ์ ํน๋ณํ ์ฒ๋ฆฌ๊ฐ ํ์ํ ๊ฒฝ์ฐ์ ์ฌ์ฉํ  ์ ์์ต๋๋ค. 
* ์ฒด์ธ ํํ์ ๊ตฌ์กฐ

![image](https://user-images.githubusercontent.com/63777714/142761605-7cfa94b0-456d-42a1-8f8a-c33406cbdcd7.png)

* Servlet Listener -> Servlet Filter -> Servlet ์์ผ๋ก ๋์ํฉ๋๋ค.  

---
# **DispatcherServlet**
## **DispatherServlet ์ด๊ธฐํ**

์๋์ ํน๋ณํ ํ์์ ๋น์ ์ฐพ๊ฑฐ๋, ๊ธฐ๋ณธ ์ ๋ต์ ํด๋นํ๋ ๋น์ ๋ฑ๋กํฉ๋๋ค.
* HandlerMapping(ํธ๋ค๋ฌ๋ฅผ ์ฐพ์์ฃผ๋ ์ธํฐํ์ด์ค)
* HanlderAdpater(ํธ๋ค๋ฌ๋ฅผ ์คํํ๋ ์ธํฐํ์ด์ค)
* HanlderExceptionResolver, ViewResolver, ....
![image](https://user-images.githubusercontent.com/63777714/143885053-b2f859d0-60f6-4a45-97d0-002c1e891fbb.png)

* ๊ธฐ๋ณธ์ ์ผ๋ก DispatcherServlet์ ์๋ฌด๋ฐ ๋ฑ๋ก์ ํ์ง ์์๋ 2๊ฐ์ `HandlerMapping` ์ด ๋ฑ๋ก๋์ด์์ต๋๋ค.
    * `BeanNameUrlHandlerMapping` , `RequestMappingHandlerMapping` ์ด ๋ฑ๋ก๋์ด ์์ต๋๋ค. 
    * `@GetMapping` ๋๋ `@PostMapping` ์ฒ๋ผ ์ด๋ธํ์ด์ ๊ธฐ๋ฐ์ผ๋ก ์ฌ์ฉํ  ์ ์๋ ๊ฒ์ `RequestMappingHandlerMapping`์ด ๊ธฐ๋ณธ์ ์ผ๋ก ๋ฑ๋ก์ด ๋์ด ์์ด์ ์ฌ์ฉ ๊ฐ๋ฅํ ๊ฒ์๋๋ค. 

* ๊ทธ ์ธ์ `DispatcherServlet.properties`์ ์ ์๋ `DispatcherServlet` ์ ๊ธฐ๋ณธ ์ ๋ต์ ๋ค์๊ณผ ๊ฐ์ต๋๋ค. 

![image](https://user-images.githubusercontent.com/63777714/143889490-f67d261c-256f-4089-afe9-c725d001ad10.png)

<br>

## **DispatherServlet ๋์ ์์**
1. ์์ฒญ์ ๋ถ์ํ๋ค. (๋ก์ผ์ผ, ํ๋ง, ๋ฉํฐํํฌ ๋ฑ๋ฑ)
2. (ํธ๋ค๋ฌ ๋งคํ์๊ฒ ์์ํ์ฌ) ์์ฒญ์ ์ฒ๋ฆฌํ  ํธ๋ค๋ฌ๋ฅผ ์ฐพ๋๋ค.
3. (๋ฑ๋ก๋์ด ์๋ ํธ๋ค๋ฌ ์ด๋ํฐ์ค) ํด๋น ํธ๋ค๋ฌ๋ฅผ ์คํผ ํ  ์ ์๋ "ํธ๋ค๋ฌ ์ด๋ํฐ"๋ฅผ ์ฐพ๋๋ค.
4. ์ฐพ์๋ธ "ํธ๋ค๋ฌ ์ด๋ํฐ"๋ฅผ ์ฌ์ฉํด์ ํธ๋ค๋ฌ์ ์๋ต์ ์ฒ๋ฆฌํ๋ค.
    * ํธ๋ค๋ฌ์ ๋ฆฌํด๊ฐ์ ๋ณด๊ณ  ์ด๋ป๊ฒ ์ฒ๋ฆฌํ ์ง๋ฅผ ํ๋จํ๋ค.
        * ๋ทฐ ์ด๋ฆ์ ํด๋นํ๋ ๋ทฐ๋ฅผ ์ฐพ์์ ๋ชจ๋ธ ๋ฐ์ดํฐ๋ฅผ ๋๋๋งํ๋ค.
        *  `@ResponseBody`๊ฐ ์๋ค๋ฉด `Converter`๋ฅผ ์ฌ์ฉํด์ ์๋ต ๋ณธ๋ฌธ์
๋ง๋ค๊ณ ,
5. (๋ถ๊ฐ์ ์ผ๋ก) ์์ธ๊ฐ ๋ฐ์ํ๋ค๋ฉด, ์์ธ ์ฒ๋ฆฌ ํธ๋ค๋ฌ์ ์์ฒญ ์ฒ๋ฆฌ๋ฅผ ์์ํ๋ค.
6. ์ต์ข์ ์ผ๋ก ์๋ต์ ๋ณด๋ธ๋ค.


<br>

### **BeanNameUrlHandlerMapping ์ ๋ต ์ฌ์ฉํ๋ ์์ **

simpleController.java (`BeanNameUrlHandlerMapping` ์ ๋ต์ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ)
```java
@org.springframework.stereotype.Controller("/simple")
public class SimpleController implements Controller {
@Override
public ModelAndView handleRequest(HttpServletRequest request,
HttpServletResponse response) throws Exception {
return new ModelAndView("/WEB-INF/simple.jsp");
}

```
์์ ์ฝ๋๋ `BeanNameUrlHandlerMapping` ๋ฅผ ์ฌ์ฉํ๋ฉฐ ์ด๋ฅผ ์คํ์์ผ์ฃผ๋ ์ด๋ํฐ๋ `SimpleControllerHandlerAdapter`์๋๋ค. 

### **ViewResolver ์ปค์คํ ์์ **
webconfig.java (`ViewResolver` ์ปค์คํ)
```java
@Configuration
@ComponentScan
public class WebConfig {
    @Bean
    public InternalResourceViewResolver viewResolver() {
        InternalResourceViewResolver viewResolver = new
        InternalResourceViewResolver(); 
        viewResolver.setPrefix("/WEB-INF/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}
```
```java
@org.springframework.stereotype.Controller("/simple")
public class SimpleController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response)    throws Exception {
        return new ModelAndView("simple"); // --> /WEB-INF/~.jsp ์๋ต ๊ฐ๋ฅ
    }
}

```
์์ ์ฝ๋๋ `ViewResolver`๋ฅผ ์ปค์คํํ ๊ฒ์ผ๋ก ๋ค์๊ณผ ๊ฐ์ด suffix, prefix๋ฅผ ๋ฑ๋กํ๋ฉด ๋ฑ๋กํ ๋ถ๋ถ ์๋ต์ด ๊ฐ๋ฅํฉ๋๋ค. 

---
## **์คํ๋ง MVC ๊ตฌ์ฑ ์์**

![image](https://user-images.githubusercontent.com/63777714/143891393-ff7e3b5b-198e-493c-a144-d896428d7e98.png)

* DispatcherSerlvet์ ๊ธฐ๋ณธ ์ ๋ต
    * DispatcherServlet.properties
* MultipartResolver
    * ํ์ผ ์๋ก๋ ์์ฒญ ์ฒ๋ฆฌ์ ํ์ํ ์ธํฐํ์ด์ค
    * HttpServletRequest๋ฅผ MultipartHttpServletRequest๋ก ๋ณํํด์ฃผ์ด ์์ฒญ์ด ๋ด๊ณ  ์๋
File์ ๊บผ๋ผ ์ ์๋ API ์ ๊ณต.
* LocaleResolver
    * ํด๋ผ์ด์ธํธ์ ์์น(Locale) ์ ๋ณด๋ฅผ ํ์ํ๋ ์ธํฐํ์ด์ค (์ง์ญ์ ๋ณด ํ์ธ)
    * ๊ธฐ๋ณธ ์ ๋ต์ ์์ฒญ์ accept-language๋ฅผ ๋ณด๊ณ  ํ๋จ.
* ThemeResolver
    * ์ ํ๋ฆฌ์ผ์ด์์ ์ค์ ๋ ํ๋ง๋ฅผ ํ์ํ๊ณ  ๋ณ๊ฒฝํ  ์ ์๋ ์ธํฐํ์ด์ค (ex.๋คํฌ๋ชจ๋)
    * ์ฐธ๊ณ : https://memorynotfound.com/spring-mvc-theme-switcher-example/
* HandlerMapping
    * ์์ฒญ์ ์ฒ๋ฆฌํ  ํธ๋ค๋ฌ๋ฅผ ์ฐพ๋ ์ธํฐํ์ด์ค 
* HandlerAdapter
    * HandlerMapping์ด ์ฐพ์๋ธ โํธ๋ค๋ฌโ๋ฅผ ์ฒ๋ฆฌํ๋ ์ธํฐํ์ด์ค
    * ์คํ๋ง MVC ํ์ฅ๋ ฅ์ ํต์ฌ
* HandlerExceptionResolver
    * ์์ฒญ ์ฒ๋ฆฌ ์ค์ ๋ฐ์ํ ์๋ฌ ์ฒ๋ฆฌํ๋ ์ธํฐํ์ด์ค
* RequestToViewNameTranslator
    * ํธ๋ค๋ฌ์์ ๋ทฐ ์ด๋ฆ์ ๋ช์์ ์ผ๋ก ๋ฆฌํดํ์ง ์์ ๊ฒฝ์ฐ, ์์ฒญ์ ๊ธฐ๋ฐ์ผ๋ก ๋ทฐ ์ด๋ฆ์
ํ๋จํ๋ ์ธํฐํ์ด์ค
* ViewResolver
    * ๋ทฐ ์ด๋ฆ(string)์ ํด๋นํ๋ ๋ทฐ๋ฅผ ์ฐพ์๋ด๋ ์ธํฐํ์ด์ค
* FlashMapManager
    * FlashMap ์ธ์คํด์ค๋ฅผ ๊ฐ์ ธ์ค๊ณ  ์ ์ฅํ๋ ์ธํฐํ์ด์ค
    * FlashMap์ ์ฃผ๋ก ๋ฆฌ๋ค์ด๋ ์์ ์ฌ์ฉํ  ๋ ์์ฒญ ๋งค๊ฐ๋ณ์๋ฅผ ์ฌ์ฉํ์ง ์๊ณ  ๋ฐ์ดํฐ๋ฅผ
์ ๋ฌํ๊ณ  ์ ๋ฆฌํ  ๋ ์ฌ์ฉํ๋ค.
    * redirect:/events?id=2019 --> redirect:/events (๋งค๊ฐ๋ณ์๋ฅผ ์ฌ์ฉํ์ง ์๊ฒ ํจ)

---
## **์ ๋ฆฌ**
* DispatcherServlet
    * DispatcherServlet ์ด๊ธฐํ
        1. ํน์  ํ์์ ํด๋นํ๋ ๋น์ ์ฐพ๋๋ค.
        2. ์์ผ๋ฉด ๊ธฐ๋ณธ ์ ๋ต์ ์ฌ์ฉํ๋ค. (DispatcherServlet.properties)
* ์คํ๋ง ๋ถํธ ์ฌ์ฉํ์ง ์๋ ์คํ๋ง MVC
    *  ์๋ธ๋ฆฟ ์ปจ๋ค์ด๋(ex, ํฐ์บฃ)์ ๋ฑ๋กํ ์น ์ ํ๋ฆฌ์ผ์ด์(WAR)์ DispatcherServlet์
๋ฑ๋กํ๋ค. 
        * web.xml์ ์๋ธ๋ฆฟ ๋ฑ๋ก
        * ๋๋ WebApplicationInitializer์ ์๋ฐ ์ฝ๋๋ก ์๋ธ๋ฆฟ ๋ฑ๋ก (์คํ๋ง 3.1+, ์๋ธ๋ฆฟ 3.0+) 
    * ์ธ๋ถ ๊ตฌ์ฑ ์์๋ ๋น ์ค์ ํ๊ธฐ ๋๋ฆ.
```java
// WebApplicationInitializer ์ฌ์ฉํ์ฌ ์๋ธ๋ฆฟ ๋ฑ๋กํ๋ ๋ฐฉ๋ฒ
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
* ์คํ๋ง ๋ถํธ๋ฅผ ์ฌ์ฉํ๋ ์คํ๋ง MVC
    * ์๋ฐ ์ ํ๋ฆฌ์ผ์ด์์ ๋ด์ฅ(์๋ฒ ๋๋) ํฐ์บฃ์ ๋ง๋ค๊ณ  ๊ทธ ์์ DispatcherServlet์
๋ฑ๋กํ๋ค. 
        * ์คํ๋ง ๋ถํธ ์๋ ์ค์ ์ด ์๋์ผ๋ก ํด์ค.
    * ์คํ๋ง ๋ถํธ์ ์ฃผ๊ด์ ๋ฐ๋ผ ์ฌ๋ฌ ์ธํฐํ์ด์ค ๊ตฌํ์ฒด๋ฅผ ๋น์ผ๋ก ๋ฑ๋กํ๋ค
