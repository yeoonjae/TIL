> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **스프링 MVC 설정_2**

## **핸들러 인터셉터**

**HandlerInterceptor**
* 핸들러 맵핑에 설정할 수 있는 인터셉터입니다. 
* 핸들러를 실행하기 전, 후(아직 랜더링 전) 그리고 완료(랜더링까지 끝난 이후) 시점에 부가 작업을 하고 싶은 경우에 사용할 수 있습니다. 
> `preHandle` : 일반적인 서블릿 filter로 서블릿 요청이 처리되기 전과 비슷하지만, arg로 들어오는 handler의 정보도 같이 제공됨으로써 좀 더 상세한 설정이 가능합니다. <br>
> 요청처리 <br>
> `postHandler` <br>
> 뷰 랜더링 <br>
> `afterCompletion`

* 여러 핸들러에서 반복적으로 사용하는 코드를 줄이고 싶을 때 사용할 수 있다.
    * 로깅, 인증 체크, Locale 변경 등...

`boolean preHandle(request, response, handler)`
*  핸들러 실행하기 전에 호출 됨
* “핸들러"에 대한 정보를 사용할 수 있기 때문에 서블릿 필터에 비해 보다 세밀한 로직을 구현할 수 있다.
* 리턴값으로 계속 다음 인터셉터 또는 핸들러로 요청,응답을 전달할지(true) 응답 처리가 이곳에서 끝났는지(false) 알린다.
> `true` : 다음 요청으로 가라 <br>
> `false` : 여기서 요청처리 끝내라

`void postHandle(request, response, modelAndView)`
* 핸들러 실행이 끝나고 아직 뷰를 랜더링 하기 이전에 호출 됨
* “뷰"에 전달할 추가적이거나 여러 핸들러에 공통적인 모델 정보를 담는데 사용할 수도 있다. 
* 이 메소드는 인터셉터 역순으로 호출된다.
* 비동기적인 요청 처리 시에는 호출되지 않는다.
* 뷰를 커스터마이징 할 수 있다. (ModelAndView를 arg로 제공받으니까)

`void afterCompletion(request, response, handler, ex)`
* 요청 처리가 완전히 끝난 뒤(뷰 랜더링 끝난 뒤)에 호출 됨
* preHandler에서 true를 리턴한 경우에만 호출 됨
* 이 메소드는 인터셉터 역순으로 호출된다.
* 비동기적인 요청 처리 시에는 호출되지 않는다.

**vs 서블릿 필터**
* 서블릿 보다 구체적인 처리가 가능
*  서블릿은 보다 일반적인 용도의 기능을 구현하는데 사용하는게 좋다.
* 스프링에 특화되어 있는 정보와 아무런 관계가 없을 때 사용
* 스프링 MVC에 특화되어있는 정보를 참고를 해야한다 --> 핸들러 인터셉터 사용하는게 맞음

### **사용예제**
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
아무런 설정도 하지 않으면 preHandle은 1,2 순서로, 나머지는 역순으로 나옵니다. 순서를 설정하고 싶다면 order()를 붙여서 설정할 수 있으며, 음수로 갈수록 우선순위가 높습니다. 위의 예제는 순서를 설정하여 실행한 예제입니다. 또한 특정 패턴에만 적용하고 싶을 땐 다음과 같이 설정하면 된다. 
```java
registry.addInterceptor(new AnotherInterceptor()).addPathPatterns("/hi").order(-1);
```

<hr>

* 참고
    - https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addInterceptors-org.springframework.web.ervlet.config.annotation.InterceptorRegistry
    * https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerInterceptor.html
    * https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/AsyncHandlerInterceptor.html
    * http://forum.spring.io/forum/spring-projects/web/20146-what-is-the-difference-between-using-a-filter-and-interceptor (스프링 개발자 Mark Fisher의 서블릿 필터와의 차이점에
대한 답변 참고)

---
## **리소스 핸들러**
이미지, 자바스크립트, CSS, HTML 파일과 같은 정적인 리소스를 처리하는 핸들러입니다. 

**디폴트(Default) 서블릿**
* 서블릿 컨테이너가 기본으로 제공하는 서블릿으로 정적인 리소스를 처리할 때 사용한다.
* https://tomcat.apache.org/tomcat-9.0-doc/default-servlet.html
스프링 MVC 리소스 핸들러 맵핑 등록
* 가장 낮은 우선 순위로 등록.
    * 다른 핸들러 맵핑이 “/” 이하 요청을 처리하도록 허용하고
    * 최종적으로 리소스 핸들러가 처리하도록.
* DefaultServletHandlerConfigurer

**리소스 핸들러 설정**
* 어떤 요청 패턴을 지원할 것인가
* 어디서 리소스를 찾을 것인가
* 캐싱
* ResourceResolver: 요청에 해당하는 리소스를 찾는 전략
    * 캐싱, 인코딩(gzip, brotli), WebJar, ...
* ResourceTransformer: 응답으로 보낼 리소스를 수정하는 전략
    * 캐싱, CSS 링크, HTML5 AppCache, ...

**스프링 부트**
* 기본 정적 리소스 핸들러와 캐싱 제공

<br>

### **사용예제**
```java
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/mobile/**").addResourceLocations("classpath:/mobile/")
            .setCacheControl(CacheControl.maxAge(10, TimeUnit.MINUTES)); // 이 리소스는 캐시 관련된 header가 응답헤더에 추가 및 응답은 10분동안 캐싱함
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
## **HTTP 메시지 컨버터**

* 요청 본문에서 메세지를 읽어들이거나 (`@RequestBody`), 응답 본문에 메세지를 작성할 때 (`@ResponseBody`) 사용합니다.

* 기본 HTTP 메세지 컨버터 : 어떤것을 사용할 것인가 결정은 Request의 Content type을 보고 판단합니다. 
    * 바이트 배열 컨버터
    * 문자열 컨버터
    * Resource 컨버터
    * Form 컨버터 (폼 데이터 to/from MultiValueMap<String, String>)
    * (JAXB2 컨버터)
    * (Jackson2 컨버터)
    * (Jackson 컨버터)
    * (Gson 컨버터)
    * (Atom 컨버터)
    * (RSS 컨버터)

*  설정방법 (`WebMvcConfigurer`를 상속받은 클래스에서 )
    * 기본으로 등록해주는 컨버터에 새로운 컨버터 추가하기: `extendMessageConverters`
    * `configureMessageConverters` 오버라이딩 하기 : 해당 메소드를 오버라이딩할 경우 컨버터 등록은 가능하지만 기본으로 제공되는 위의 컨버터들은 등록이 되지 않습니다. 
    * 기본으로 등록해주는 컨버터는 다 무시하고 새로 컨버터 설정하기:configureMessageConverters 
    * <u>의존성 추가로 컨버터 등록하기 (추천)</u>
        * <u>메이븐 또는 그래들 설정에 의존성을 추가하면 그에 따른 컨버터가 자동으로 등록 된다. </u>
        * <u> WebMvcConfigurationSupport </u>
        * (이 기능 자체는 스프링 프레임워크의 기능임, 스프링 부트 아님.)

* HTTP JSON Converter 사용하기

WebMvcConfigurationSupport.class

![image](https://user-images.githubusercontent.com/63777714/144029185-762bcff4-3973-45c8-b2ed-f9078981e889.png)

위 사진은 WebMvcConfigurationSupport.class 파일의 일부입니다. 이처럼 classpath에 해당하는 라이브러리가 존재하는 경우에만 해당하는 Converter를 등록해줍니다. 

* 스프링 부트를 사용하는 경우엔 기본적으로 JacksonJSON2가 의존성으로 추가됩니다. 때문에 스프링부트를 사용할 경우엔 의존성을 따로 추가하지 않아도 됩니다. 
* 스프링 부트가 아닌 경우엔 사용하고자 하는 JSON 라이브러리를 의존성으로 추가하면 됩니다.
    * GSON
    * JacksonJSON
    * JacksonJSON 2

### **예제코드**
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
    ObjectMapper objectMapper; // Jackson이 제공하는 것으로 json으로 변환해줍니다.
    
    @Test
    public void jsonMessage() throws Exception {
        Person person = new Person();
        person.setId(2021l);
        person.setName("yeonjae");
        String jsonString = objectMapper.writeValueAsString(person);

        this.mockMvc.perform(get("/jsonMessage")
                .contentType(MediaType.APPLICATION_JSON) // 요청을 보낼 데이터 타입을 지정
                .accept(MediaType.APPLICATION_JSON) // 응답에 대한 요청으로 원하는 데이터 타입을 지정
                .content(jsonString))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.id").value(2021))
            .andExpect(jsonPath("$.name").value("yeonjae"));;
    }
}
```
---
## **HTTP XML 메시지 컨버터**

XML 메세지 컨버터를 사용하려면 OXM(Object-XML Mapper) 라이브러리 중에 스프링이 지원하는 의존성 추가해야 합니다. 
* JacksonXML
* JAXB

의존성 코드는 다음과 같습니다. 
```xml
<!--JaxB 의존성 추가-->
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
JaxB 같은 경우엔 스프링 부트를 사용하더라도 기본으로 의존성을 추가해주지 않습니다. 그러니 xml 메세지 컨버터를 사용하려면 의존성 추가가 필요합니다. 

### **예제코드**

```java
@XmlRootElement
@Entity
public class Person {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    // ...getter/setter 생략
}
```
```java
package study.springboot.demobootweb;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Bean
    public Jaxb2Marshaller jaxb2Marshaller() {
        Jaxb2Marshaller jaxb2Marshaller = new Jaxb2Marshaller();
        jaxb2Marshaller.setPackagesToScan(Person.class.getPackageName());// @XmlRootElement가 붙어있는 클래스를 알려줘야함
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
                .contentType(MediaType.APPLICATION_XML) // 요청을 보낼 데이터 타입을 지정
                .accept(MediaType.APPLICATION_XML) // 응답에 대한 요청으로 원하는 데이터 타입을 지정
                .content(xmlString))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(xpath("person/name").string("yeonjae"))
            .andExpect(xpath("person/id").string("2021"));
    }
}
```
---

## **그 밖에 WebMvcConfigurer 설정**

**CORS 설정**
* Cross Origin 요청 처리 설정
* 같은 도메인에서 온 요청이 아니더라도 처리를 허용하고 싶다면 설정합니다.

**리턴 값 핸들러 설정**
* 스프링 MVC가 제공하는 기본 리턴 값 핸들러 이외에 리턴 핸들러를 추가하고 싶을 때
설정합니다.

**아큐먼트 리졸버 설정**
* 스프링 MVC가 제공하는 기본 아규먼트 리졸버 이외에 커스텀한 아규먼트 리졸버를
추가하고 싶을 때 설정합니다.

**뷰 컨트롤러**
* 단순하게 요청 URL을 특정 뷰로 연결하고 싶을 때 사용할 수 있습니다.

**비동기 설정**
* 비동기 요청 처리에 사용할 타임아웃이나 TaskExecutor를 설정할 수 있습니다.

**뷰 리졸버 설정**
* 핸들러에서 리턴하는 뷰 이름에 해당하는 문자열을 View 인스턴스로 바꿔줄 뷰 리졸버를
설정합니다. 

**Content Negotiation 설정**
* 요청 본문 또는 응답 본문을 어떤 (MIME) 타입으로 보내야 하는지 결정하는 전략을
설정할 수 있습니다. 

---
## **스프링 MVC 설정 정리**
* **스프링 MVC 설정은 즉 DispatcherServlet이 사용할 여러 빈 설정.**
    - HandlerMapper
    - HandlerAdapter
    - ViewResolver
    - ExceptionResolver
    - LocaleResolver
    - ...
* 일일히 등록하려니 너무 많고, 해당 빈들이 참조하는 또 다른 객체들까지 설정하려면... 설정할게 너무 많다.

* `@EnableWebMvc`
    - 애노테이션 기반의 스프링 MVC 설정 간편화
    - `WebMvcConfigurer`가 제공하는 메소드를 구현하여 커스터마이징할 수 있다.
* **스프링 부트**
    - 스프링 부트 자동 설정을 통해 다양한 스프링 MVC 기능을 아무런 설정 파일을
    만들지 않아도 제공한다.
    - `WebMvcConfigurer`가 제공하는 메소드를 구현하여 커스터마이징할 수
    있다. 
    - `@EnableWebMvc`를 사용하면 스프링 부트 자동 설정을 사용하지
    못한다.
* **스프링 MVC 설정 방법**
- 스프링 부트를 사용하는 경우에는 application.properties 부터 시작.
- WebMvcConfigurer로 시작
- @Bean으로 MVC 구성 요소 직접 등록
