> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ์น MVC'๋ฅผ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>

# **์คํ๋ง MVC ์ค์ _2**

## **ํธ๋ค๋ฌ ์ธํฐ์ํฐ**

**HandlerInterceptor**
* ํธ๋ค๋ฌ ๋งตํ์ ์ค์ ํ  ์ ์๋ ์ธํฐ์ํฐ์๋๋ค. 
* ํธ๋ค๋ฌ๋ฅผ ์คํํ๊ธฐ ์ , ํ(์์ง ๋๋๋ง ์ ) ๊ทธ๋ฆฌ๊ณ  ์๋ฃ(๋๋๋ง๊น์ง ๋๋ ์ดํ) ์์ ์ ๋ถ๊ฐ ์์์ ํ๊ณ  ์ถ์ ๊ฒฝ์ฐ์ ์ฌ์ฉํ  ์ ์์ต๋๋ค. 
> `preHandle` : ์ผ๋ฐ์ ์ธ ์๋ธ๋ฆฟ filter๋ก ์๋ธ๋ฆฟ ์์ฒญ์ด ์ฒ๋ฆฌ๋๊ธฐ ์ ๊ณผ ๋น์ทํ์ง๋ง, arg๋ก ๋ค์ด์ค๋ handler์ ์ ๋ณด๋ ๊ฐ์ด ์ ๊ณต๋จ์ผ๋ก์จ ์ข ๋ ์์ธํ ์ค์ ์ด ๊ฐ๋ฅํฉ๋๋ค. <br>
> ์์ฒญ์ฒ๋ฆฌ <br>
> `postHandler` <br>
> ๋ทฐ ๋๋๋ง <br>
> `afterCompletion`

* ์ฌ๋ฌ ํธ๋ค๋ฌ์์ ๋ฐ๋ณต์ ์ผ๋ก ์ฌ์ฉํ๋ ์ฝ๋๋ฅผ ์ค์ด๊ณ  ์ถ์ ๋ ์ฌ์ฉํ  ์ ์๋ค.
    * ๋ก๊น, ์ธ์ฆ ์ฒดํฌ, Locale ๋ณ๊ฒฝ ๋ฑ...

`boolean preHandle(request, response, handler)`
*  ํธ๋ค๋ฌ ์คํํ๊ธฐ ์ ์ ํธ์ถ ๋จ
* โํธ๋ค๋ฌ"์ ๋ํ ์ ๋ณด๋ฅผ ์ฌ์ฉํ  ์ ์๊ธฐ ๋๋ฌธ์ ์๋ธ๋ฆฟ ํํฐ์ ๋นํด ๋ณด๋ค ์ธ๋ฐํ ๋ก์ง์ ๊ตฌํํ  ์ ์๋ค.
* ๋ฆฌํด๊ฐ์ผ๋ก ๊ณ์ ๋ค์ ์ธํฐ์ํฐ ๋๋ ํธ๋ค๋ฌ๋ก ์์ฒญ,์๋ต์ ์ ๋ฌํ ์ง(true) ์๋ต ์ฒ๋ฆฌ๊ฐ ์ด๊ณณ์์ ๋๋ฌ๋์ง(false) ์๋ฆฐ๋ค.
> `true` : ๋ค์ ์์ฒญ์ผ๋ก ๊ฐ๋ผ <br>
> `false` : ์ฌ๊ธฐ์ ์์ฒญ์ฒ๋ฆฌ ๋๋ด๋ผ

`void postHandle(request, response, modelAndView)`
* ํธ๋ค๋ฌ ์คํ์ด ๋๋๊ณ  ์์ง ๋ทฐ๋ฅผ ๋๋๋ง ํ๊ธฐ ์ด์ ์ ํธ์ถ ๋จ
* โ๋ทฐ"์ ์ ๋ฌํ  ์ถ๊ฐ์ ์ด๊ฑฐ๋ ์ฌ๋ฌ ํธ๋ค๋ฌ์ ๊ณตํต์ ์ธ ๋ชจ๋ธ ์ ๋ณด๋ฅผ ๋ด๋๋ฐ ์ฌ์ฉํ  ์๋ ์๋ค. 
* ์ด ๋ฉ์๋๋ ์ธํฐ์ํฐ ์ญ์์ผ๋ก ํธ์ถ๋๋ค.
* ๋น๋๊ธฐ์ ์ธ ์์ฒญ ์ฒ๋ฆฌ ์์๋ ํธ์ถ๋์ง ์๋๋ค.
* ๋ทฐ๋ฅผ ์ปค์คํฐ๋ง์ด์ง ํ  ์ ์๋ค. (ModelAndView๋ฅผ arg๋ก ์ ๊ณต๋ฐ์ผ๋๊น)

`void afterCompletion(request, response, handler, ex)`
* ์์ฒญ ์ฒ๋ฆฌ๊ฐ ์์ ํ ๋๋ ๋ค(๋ทฐ ๋๋๋ง ๋๋ ๋ค)์ ํธ์ถ ๋จ
* preHandler์์ true๋ฅผ ๋ฆฌํดํ ๊ฒฝ์ฐ์๋ง ํธ์ถ ๋จ
* ์ด ๋ฉ์๋๋ ์ธํฐ์ํฐ ์ญ์์ผ๋ก ํธ์ถ๋๋ค.
* ๋น๋๊ธฐ์ ์ธ ์์ฒญ ์ฒ๋ฆฌ ์์๋ ํธ์ถ๋์ง ์๋๋ค.

**vs ์๋ธ๋ฆฟ ํํฐ**
* ์๋ธ๋ฆฟ ๋ณด๋ค ๊ตฌ์ฒด์ ์ธ ์ฒ๋ฆฌ๊ฐ ๊ฐ๋ฅ
*  ์๋ธ๋ฆฟ์ ๋ณด๋ค ์ผ๋ฐ์ ์ธ ์ฉ๋์ ๊ธฐ๋ฅ์ ๊ตฌํํ๋๋ฐ ์ฌ์ฉํ๋๊ฒ ์ข๋ค.
* ์คํ๋ง์ ํนํ๋์ด ์๋ ์ ๋ณด์ ์๋ฌด๋ฐ ๊ด๊ณ๊ฐ ์์ ๋ ์ฌ์ฉ
* ์คํ๋ง MVC์ ํนํ๋์ด์๋ ์ ๋ณด๋ฅผ ์ฐธ๊ณ ๋ฅผ ํด์ผํ๋ค --> ํธ๋ค๋ฌ ์ธํฐ์ํฐ ์ฌ์ฉํ๋๊ฒ ๋ง์

### **์ฌ์ฉ์์ **
```java

public class GreetingInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
        Object handler) throws Exception {
        System.out.println("preHandle 1");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle 1");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
        Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion 1");
    }
}

```
```java
public class AnotherInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
        Object handler) throws Exception {
        System.out.println("preHandle 2");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle 2");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
        Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion 2");
    }
}
```
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new GreetingInterceptor()).order(0);
        registry.addInterceptor(new AnotherInterceptor()).order(-1);
    }
}
```
```
preHandle 2
preHandle 1
postHandle 1
postHandle 2
afterCompletion 1
afterCompletion 2
```
์๋ฌด๋ฐ ์ค์ ๋ ํ์ง ์์ผ๋ฉด preHandle์ 1,2 ์์๋ก, ๋๋จธ์ง๋ ์ญ์์ผ๋ก ๋์ต๋๋ค. ์์๋ฅผ ์ค์ ํ๊ณ  ์ถ๋ค๋ฉด order()๋ฅผ ๋ถ์ฌ์ ์ค์ ํ  ์ ์์ผ๋ฉฐ, ์์๋ก ๊ฐ์๋ก ์ฐ์ ์์๊ฐ ๋์ต๋๋ค. ์์ ์์ ๋ ์์๋ฅผ ์ค์ ํ์ฌ ์คํํ ์์ ์๋๋ค. ๋ํ ํน์  ํจํด์๋ง ์ ์ฉํ๊ณ  ์ถ์ ๋ ๋ค์๊ณผ ๊ฐ์ด ์ค์ ํ๋ฉด ๋๋ค. 
```java
registry.addInterceptor(new AnotherInterceptor()).addPathPatterns("/hi").order(-1);
```

<hr>

* ์ฐธ๊ณ 
    - https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addInterceptors-org.springframework.web.ervlet.config.annotation.InterceptorRegistry
    * https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerInterceptor.html
    * https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/AsyncHandlerInterceptor.html
    * http://forum.spring.io/forum/spring-projects/web/20146-what-is-the-difference-between-using-a-filter-and-interceptor (์คํ๋ง ๊ฐ๋ฐ์ Mark Fisher์ ์๋ธ๋ฆฟ ํํฐ์์ ์ฐจ์ด์ ์
๋ํ ๋ต๋ณ ์ฐธ๊ณ )

---
## **๋ฆฌ์์ค ํธ๋ค๋ฌ**
์ด๋ฏธ์ง, ์๋ฐ์คํฌ๋ฆฝํธ, CSS, HTML ํ์ผ๊ณผ ๊ฐ์ ์ ์ ์ธ ๋ฆฌ์์ค๋ฅผ ์ฒ๋ฆฌํ๋ ํธ๋ค๋ฌ์๋๋ค. 

**๋ํดํธ(Default) ์๋ธ๋ฆฟ**
* ์๋ธ๋ฆฟ ์ปจํ์ด๋๊ฐ ๊ธฐ๋ณธ์ผ๋ก ์ ๊ณตํ๋ ์๋ธ๋ฆฟ์ผ๋ก ์ ์ ์ธ ๋ฆฌ์์ค๋ฅผ ์ฒ๋ฆฌํ  ๋ ์ฌ์ฉํ๋ค.
* https://tomcat.apache.org/tomcat-9.0-doc/default-servlet.html
์คํ๋ง MVC ๋ฆฌ์์ค ํธ๋ค๋ฌ ๋งตํ ๋ฑ๋ก
* ๊ฐ์ฅ ๋ฎ์ ์ฐ์  ์์๋ก ๋ฑ๋ก.
    * ๋ค๋ฅธ ํธ๋ค๋ฌ ๋งตํ์ด โ/โ ์ดํ ์์ฒญ์ ์ฒ๋ฆฌํ๋๋ก ํ์ฉํ๊ณ 
    * ์ต์ข์ ์ผ๋ก ๋ฆฌ์์ค ํธ๋ค๋ฌ๊ฐ ์ฒ๋ฆฌํ๋๋ก.
* DefaultServletHandlerConfigurer

**๋ฆฌ์์ค ํธ๋ค๋ฌ ์ค์ **
* ์ด๋ค ์์ฒญ ํจํด์ ์ง์ํ  ๊ฒ์ธ๊ฐ
* ์ด๋์ ๋ฆฌ์์ค๋ฅผ ์ฐพ์ ๊ฒ์ธ๊ฐ
* ์บ์ฑ
* ResourceResolver: ์์ฒญ์ ํด๋นํ๋ ๋ฆฌ์์ค๋ฅผ ์ฐพ๋ ์ ๋ต
    * ์บ์ฑ, ์ธ์ฝ๋ฉ(gzip, brotli), WebJar, ...
* ResourceTransformer: ์๋ต์ผ๋ก ๋ณด๋ผ ๋ฆฌ์์ค๋ฅผ ์์ ํ๋ ์ ๋ต
    * ์บ์ฑ, CSS ๋งํฌ, HTML5 AppCache, ...

**์คํ๋ง ๋ถํธ**
* ๊ธฐ๋ณธ ์ ์  ๋ฆฌ์์ค ํธ๋ค๋ฌ์ ์บ์ฑ ์ ๊ณต

<br>

### **์ฌ์ฉ์์ **
```java
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/mobile/**").addResourceLocations("classpath:/mobile/")
            .setCacheControl(CacheControl.maxAge(10, TimeUnit.MINUTES)); // ์ด ๋ฆฌ์์ค๋ ์บ์ ๊ด๋ จ๋ header๊ฐ ์๋ตํค๋์ ์ถ๊ฐ ๋ฐ ์๋ต์ 10๋ถ๋์ ์บ์ฑํจ
    }
```
```java
    @Test
    public void helloStatic() throws Exception {
        this.mockMvc.perform(get("/mobile/index.html"))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(content().string(Matchers.containsString("Hello Mobile")))
            .andExpect(header().exists(HttpHeaders.CACHE_CONTROL));
    }

```
---
## **HTTP ๋ฉ์์ง ์ปจ๋ฒํฐ**

* ์์ฒญ ๋ณธ๋ฌธ์์ ๋ฉ์ธ์ง๋ฅผ ์ฝ์ด๋ค์ด๊ฑฐ๋ (`@RequestBody`), ์๋ต ๋ณธ๋ฌธ์ ๋ฉ์ธ์ง๋ฅผ ์์ฑํ  ๋ (`@ResponseBody`) ์ฌ์ฉํฉ๋๋ค.

* ๊ธฐ๋ณธ HTTP ๋ฉ์ธ์ง ์ปจ๋ฒํฐ : ์ด๋ค๊ฒ์ ์ฌ์ฉํ  ๊ฒ์ธ๊ฐ ๊ฒฐ์ ์ Request์ Content type์ ๋ณด๊ณ  ํ๋จํฉ๋๋ค. 
    * ๋ฐ์ดํธ ๋ฐฐ์ด ์ปจ๋ฒํฐ
    * ๋ฌธ์์ด ์ปจ๋ฒํฐ
    * Resource ์ปจ๋ฒํฐ
    * Form ์ปจ๋ฒํฐ (ํผ ๋ฐ์ดํฐ to/from MultiValueMap<String, String>)
    * (JAXB2 ์ปจ๋ฒํฐ)
    * (Jackson2 ์ปจ๋ฒํฐ)
    * (Jackson ์ปจ๋ฒํฐ)
    * (Gson ์ปจ๋ฒํฐ)
    * (Atom ์ปจ๋ฒํฐ)
    * (RSS ์ปจ๋ฒํฐ)

*  ์ค์ ๋ฐฉ๋ฒ (`WebMvcConfigurer`๋ฅผ ์์๋ฐ์ ํด๋์ค์์ )
    * ๊ธฐ๋ณธ์ผ๋ก ๋ฑ๋กํด์ฃผ๋ ์ปจ๋ฒํฐ์ ์๋ก์ด ์ปจ๋ฒํฐ ์ถ๊ฐํ๊ธฐ: `extendMessageConverters`
    * `configureMessageConverters` ์ค๋ฒ๋ผ์ด๋ฉ ํ๊ธฐ : ํด๋น ๋ฉ์๋๋ฅผ ์ค๋ฒ๋ผ์ด๋ฉํ  ๊ฒฝ์ฐ ์ปจ๋ฒํฐ ๋ฑ๋ก์ ๊ฐ๋ฅํ์ง๋ง ๊ธฐ๋ณธ์ผ๋ก ์ ๊ณต๋๋ ์์ ์ปจ๋ฒํฐ๋ค์ ๋ฑ๋ก์ด ๋์ง ์์ต๋๋ค. 
    * ๊ธฐ๋ณธ์ผ๋ก ๋ฑ๋กํด์ฃผ๋ ์ปจ๋ฒํฐ๋ ๋ค ๋ฌด์ํ๊ณ  ์๋ก ์ปจ๋ฒํฐ ์ค์ ํ๊ธฐ:configureMessageConverters 
    * <u>์์กด์ฑ ์ถ๊ฐ๋ก ์ปจ๋ฒํฐ ๋ฑ๋กํ๊ธฐ (์ถ์ฒ)</u>
        * <u>๋ฉ์ด๋ธ ๋๋ ๊ทธ๋๋ค ์ค์ ์ ์์กด์ฑ์ ์ถ๊ฐํ๋ฉด ๊ทธ์ ๋ฐ๋ฅธ ์ปจ๋ฒํฐ๊ฐ ์๋์ผ๋ก ๋ฑ๋ก ๋๋ค. </u>
        * <u> WebMvcConfigurationSupport </u>
        * (์ด ๊ธฐ๋ฅ ์์ฒด๋ ์คํ๋ง ํ๋ ์์ํฌ์ ๊ธฐ๋ฅ์, ์คํ๋ง ๋ถํธ ์๋.)

* HTTP JSON Converter ์ฌ์ฉํ๊ธฐ

WebMvcConfigurationSupport.class

![image](https://user-images.githubusercontent.com/63777714/144029185-762bcff4-3973-45c8-b2ed-f9078981e889.png)

์ ์ฌ์ง์ WebMvcConfigurationSupport.class ํ์ผ์ ์ผ๋ถ์๋๋ค. ์ด์ฒ๋ผ classpath์ ํด๋นํ๋ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๊ฐ ์กด์ฌํ๋ ๊ฒฝ์ฐ์๋ง ํด๋นํ๋ Converter๋ฅผ ๋ฑ๋กํด์ค๋๋ค. 

* ์คํ๋ง ๋ถํธ๋ฅผ ์ฌ์ฉํ๋ ๊ฒฝ์ฐ์ ๊ธฐ๋ณธ์ ์ผ๋ก JacksonJSON2๊ฐ ์์กด์ฑ์ผ๋ก ์ถ๊ฐ๋ฉ๋๋ค. ๋๋ฌธ์ ์คํ๋ง๋ถํธ๋ฅผ ์ฌ์ฉํ  ๊ฒฝ์ฐ์ ์์กด์ฑ์ ๋ฐ๋ก ์ถ๊ฐํ์ง ์์๋ ๋ฉ๋๋ค. 
* ์คํ๋ง ๋ถํธ๊ฐ ์๋ ๊ฒฝ์ฐ์ ์ฌ์ฉํ๊ณ ์ ํ๋ JSON ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ์์กด์ฑ์ผ๋ก ์ถ๊ฐํ๋ฉด ๋ฉ๋๋ค.
    * GSON
    * JacksonJSON
    * JacksonJSON 2

### **์์ ์ฝ๋**
```java

    @GetMapping("/jsonMessage")
    public Person jsonMessage(@RequestBody Person person) {
        return person;
    }
```
```java
@ExtendWith(SpringExtension.class)
@SpringBootTest
@AutoConfigureMockMvc
public class SampleControllerTest {

    @Autowired
    ObjectMapper objectMapper; // Jackson์ด ์ ๊ณตํ๋ ๊ฒ์ผ๋ก json์ผ๋ก ๋ณํํด์ค๋๋ค.
    
    @Test
    public void jsonMessage() throws Exception {
        Person person = new Person();
        person.setId(2021l);
        person.setName("yeonjae");
        String jsonString = objectMapper.writeValueAsString(person);

        this.mockMvc.perform(get("/jsonMessage")
                .contentType(MediaType.APPLICATION_JSON) // ์์ฒญ์ ๋ณด๋ผ ๋ฐ์ดํฐ ํ์์ ์ง์ 
                .accept(MediaType.APPLICATION_JSON) // ์๋ต์ ๋ํ ์์ฒญ์ผ๋ก ์ํ๋ ๋ฐ์ดํฐ ํ์์ ์ง์ 
                .content(jsonString))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.id").value(2021))
            .andExpect(jsonPath("$.name").value("yeonjae"));;
    }
}
```
---
## **HTTP XML ๋ฉ์์ง ์ปจ๋ฒํฐ**

XML ๋ฉ์ธ์ง ์ปจ๋ฒํฐ๋ฅผ ์ฌ์ฉํ๋ ค๋ฉด OXM(Object-XML Mapper) ๋ผ์ด๋ธ๋ฌ๋ฆฌ ์ค์ ์คํ๋ง์ด ์ง์ํ๋ ์์กด์ฑ ์ถ๊ฐํด์ผ ํฉ๋๋ค. 
* JacksonXML
* JAXB

์์กด์ฑ ์ฝ๋๋ ๋ค์๊ณผ ๊ฐ์ต๋๋ค. 
```xml
<!--JaxB ์์กด์ฑ ์ถ๊ฐ-->
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
</dependency>
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-oxm</artifactId>
    <version>${spring-framework.version}</version>
</dependency>
```
JaxB ๊ฐ์ ๊ฒฝ์ฐ์ ์คํ๋ง ๋ถํธ๋ฅผ ์ฌ์ฉํ๋๋ผ๋ ๊ธฐ๋ณธ์ผ๋ก ์์กด์ฑ์ ์ถ๊ฐํด์ฃผ์ง ์์ต๋๋ค. ๊ทธ๋ฌ๋ xml ๋ฉ์ธ์ง ์ปจ๋ฒํฐ๋ฅผ ์ฌ์ฉํ๋ ค๋ฉด ์์กด์ฑ ์ถ๊ฐ๊ฐ ํ์ํฉ๋๋ค. 

### **์์ ์ฝ๋**

```java
@XmlRootElement
@Entity
public class Person {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    // ...getter/setter ์๋ต
}
```
```java
package study.springboot.demobootweb;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Bean
    public Jaxb2Marshaller jaxb2Marshaller() {
        Jaxb2Marshaller jaxb2Marshaller = new Jaxb2Marshaller();
        jaxb2Marshaller.setPackagesToScan(Person.class.getPackageName());// @XmlRootElement๊ฐ ๋ถ์ด์๋ ํด๋์ค๋ฅผ ์๋ ค์ค์ผํจ
        return jaxb2Marshaller;
    }

}

```
```java
package study.springboot.demobootweb;

@ExtendWith(SpringExtension.class)
@SpringBootTest
@AutoConfigureMockMvc
public class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Autowired
    Jaxb2Marshaller jaxb2Marshaller;


    @Test
    public void xmlMessage() throws Exception {
        Person person = new Person();
        person.setId(2021l);
        person.setName("yeonjae");

        StringWriter stringWriter = new StringWriter();
        Result result = new StreamResult(stringWriter);
        jaxb2Marshaller.marshal(person,result);
        String xmlString = stringWriter.toString();


        this.mockMvc.perform(get("/jsonMessage")
                .contentType(MediaType.APPLICATION_XML) // ์์ฒญ์ ๋ณด๋ผ ๋ฐ์ดํฐ ํ์์ ์ง์ 
                .accept(MediaType.APPLICATION_XML) // ์๋ต์ ๋ํ ์์ฒญ์ผ๋ก ์ํ๋ ๋ฐ์ดํฐ ํ์์ ์ง์ 
                .content(xmlString))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(xpath("person/name").string("yeonjae"))
            .andExpect(xpath("person/id").string("2021"));
    }
}
```
---

## **๊ทธ ๋ฐ์ WebMvcConfigurer ์ค์ **

**CORS ์ค์ **
* Cross Origin ์์ฒญ ์ฒ๋ฆฌ ์ค์ 
* ๊ฐ์ ๋๋ฉ์ธ์์ ์จ ์์ฒญ์ด ์๋๋๋ผ๋ ์ฒ๋ฆฌ๋ฅผ ํ์ฉํ๊ณ  ์ถ๋ค๋ฉด ์ค์ ํฉ๋๋ค.

**๋ฆฌํด ๊ฐ ํธ๋ค๋ฌ ์ค์ **
* ์คํ๋ง MVC๊ฐ ์ ๊ณตํ๋ ๊ธฐ๋ณธ ๋ฆฌํด ๊ฐ ํธ๋ค๋ฌ ์ด์ธ์ ๋ฆฌํด ํธ๋ค๋ฌ๋ฅผ ์ถ๊ฐํ๊ณ  ์ถ์ ๋
์ค์ ํฉ๋๋ค.

**์ํ๋จผํธ ๋ฆฌ์กธ๋ฒ ์ค์ **
* ์คํ๋ง MVC๊ฐ ์ ๊ณตํ๋ ๊ธฐ๋ณธ ์๊ท๋จผํธ ๋ฆฌ์กธ๋ฒ ์ด์ธ์ ์ปค์คํํ ์๊ท๋จผํธ ๋ฆฌ์กธ๋ฒ๋ฅผ
์ถ๊ฐํ๊ณ  ์ถ์ ๋ ์ค์ ํฉ๋๋ค.

**๋ทฐ ์ปจํธ๋กค๋ฌ**
* ๋จ์ํ๊ฒ ์์ฒญ URL์ ํน์  ๋ทฐ๋ก ์ฐ๊ฒฐํ๊ณ  ์ถ์ ๋ ์ฌ์ฉํ  ์ ์์ต๋๋ค.

**๋น๋๊ธฐ ์ค์ **
* ๋น๋๊ธฐ ์์ฒญ ์ฒ๋ฆฌ์ ์ฌ์ฉํ  ํ์์์์ด๋ TaskExecutor๋ฅผ ์ค์ ํ  ์ ์์ต๋๋ค.

**๋ทฐ ๋ฆฌ์กธ๋ฒ ์ค์ **
* ํธ๋ค๋ฌ์์ ๋ฆฌํดํ๋ ๋ทฐ ์ด๋ฆ์ ํด๋นํ๋ ๋ฌธ์์ด์ View ์ธ์คํด์ค๋ก ๋ฐ๊ฟ์ค ๋ทฐ ๋ฆฌ์กธ๋ฒ๋ฅผ
์ค์ ํฉ๋๋ค. 

**Content Negotiation ์ค์ **
* ์์ฒญ ๋ณธ๋ฌธ ๋๋ ์๋ต ๋ณธ๋ฌธ์ ์ด๋ค (MIME) ํ์์ผ๋ก ๋ณด๋ด์ผ ํ๋์ง ๊ฒฐ์ ํ๋ ์ ๋ต์
์ค์ ํ  ์ ์์ต๋๋ค. 

---
## **์คํ๋ง MVC ์ค์  ์ ๋ฆฌ**
* **์คํ๋ง MVC ์ค์ ์ ์ฆ DispatcherServlet์ด ์ฌ์ฉํ  ์ฌ๋ฌ ๋น ์ค์ .**
    - HandlerMapper
    - HandlerAdapter
    - ViewResolver
    - ExceptionResolver
    - LocaleResolver
    - ...
* ์ผ์ผํ ๋ฑ๋กํ๋ ค๋ ๋๋ฌด ๋ง๊ณ , ํด๋น ๋น๋ค์ด ์ฐธ์กฐํ๋ ๋ ๋ค๋ฅธ ๊ฐ์ฒด๋ค๊น์ง ์ค์ ํ๋ ค๋ฉด... ์ค์ ํ ๊ฒ ๋๋ฌด ๋ง๋ค.

* `@EnableWebMvc`
    - ์ ๋ธํ์ด์ ๊ธฐ๋ฐ์ ์คํ๋ง MVC ์ค์  ๊ฐํธํ
    - `WebMvcConfigurer`๊ฐ ์ ๊ณตํ๋ ๋ฉ์๋๋ฅผ ๊ตฌํํ์ฌ ์ปค์คํฐ๋ง์ด์งํ  ์ ์๋ค.
* **์คํ๋ง ๋ถํธ**
    - ์คํ๋ง ๋ถํธ ์๋ ์ค์ ์ ํตํด ๋ค์ํ ์คํ๋ง MVC ๊ธฐ๋ฅ์ ์๋ฌด๋ฐ ์ค์  ํ์ผ์
    ๋ง๋ค์ง ์์๋ ์ ๊ณตํ๋ค.
    - `WebMvcConfigurer`๊ฐ ์ ๊ณตํ๋ ๋ฉ์๋๋ฅผ ๊ตฌํํ์ฌ ์ปค์คํฐ๋ง์ด์งํ  ์
    ์๋ค. 
    - `@EnableWebMvc`๋ฅผ ์ฌ์ฉํ๋ฉด ์คํ๋ง ๋ถํธ ์๋ ์ค์ ์ ์ฌ์ฉํ์ง
    ๋ชปํ๋ค.
* **์คํ๋ง MVC ์ค์  ๋ฐฉ๋ฒ**
- ์คํ๋ง ๋ถํธ๋ฅผ ์ฌ์ฉํ๋ ๊ฒฝ์ฐ์๋ application.properties ๋ถํฐ ์์.
- WebMvcConfigurer๋ก ์์
- @Bean์ผ๋ก MVC ๊ตฌ์ฑ ์์ ์ง์  ๋ฑ๋ก
