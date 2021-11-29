# **Servlet**

* **서블릿 생명 주기**
    - 서블릿 컨테이너가 서블릿 인스턴스의 `init()` 메소드를 호출하여 초기화 합니다. 
        - 최초 요청을 받았을 때 한 번 초기화 하고 나면 그 다음 요청부터는 이 과정을 생략합니다. 
    - 서블릿이 초기화 된 다음부터 클라이언트의 요청을 처리할 수 있습니다. 각 요청은 별도의 쓰레드로 처리하고 이 때 서블릿 인스턴스의 `service()` 메소드를 호출합니다.
        - 이 안에서 HTTP 요청을 받고, 클라이언트로 보낼 HTTP 응답을 만듭니다.
        - `service()` 는 보통 HTTP Method 에 따라 `doGet()`, `doPost()` 등으로 처리합니다.
        - 따라서 보통 `doGet()` 또는 `doPost()` 를 구현합니다.
    - 서블릿 컨테이너 판단에 따라 해당 서블릿을메모리에서 내려야 할 시점에 `destroy()`를 호출합니다.

---
# **Servlet Listener와 Servlet Filter**

## **Servlet Listener**
* 웹 애플리케이션에서 발생하는 주요 이벤트를 감지하고 각 이벤트에 특별한 작업이 필요한 경우에 사용할 수 있습니다. 
    * Servlet Context 수준의 이벤트
        * 컨텍스트 라이프사이클 이벤트
        * 컨텍스트 애트리뷰트 변경 이벤트
    * 세션 수준의 이벤트
        * 세션 라이프사이클 이벤트
        * 세션 애트리뷰트 변경 이벤트

## **Servlet Filter**
* 들어온 요청을 서블릿으로 보내고, 또 서블릿이 작성한 응답을 클라이언트로 보내기 전에 특별한 처리가 필요한 경우에 사용할 수 있습니다. 
* 체인 형태의 구조

![image](https://user-images.githubusercontent.com/63777714/142761605-7cfa94b0-456d-42a1-8f8a-c33406cbdcd7.png)

* Servlet Listener -> Servlet Filter -> Servlet 순으로 동작합니다.  

---
# **DispatcherServlet**
## **DispatherServlet 초기화**

아래의 특별한 타입의 빈을 찾거나, 기본 전략에 해당하는 빈을 등록합니다.
* HandlerMapping(핸들러를 찾아주는 인터페이스)
* HanlderAdpater(핸들러를 실행하는 인터페이스)
* HanlderExceptionResolver, ViewResolver, ....
![image](https://user-images.githubusercontent.com/63777714/143885053-b2f859d0-60f6-4a45-97d0-002c1e891fbb.png)

* 기본적으로 DispatcherServlet은 아무런 등록을 하지 않아도 2개의 `HandlerMapping` 이 등록되어있습니다.
    * `BeanNameUrlHandlerMapping` , `RequestMappingHandlerMapping` 이 등록되어 있습니다. 
    * `@GetMapping` 또는 `@PostMapping` 처럼 어노테이션 기반으로 사용할 수 있는 것은 `RequestMappingHandlerMapping`이 기본적으로 등록이 되어 있어서 사용 가능한 것입니다. 

* 그 외에 `DispatcherServlet.properties`에 정의된 `DispatcherServlet` 의 기본 전략은 다음과 같습니다. 

![image](https://user-images.githubusercontent.com/63777714/143889490-f67d261c-256f-4089-afe9-c725d001ad10.png)

<br>

## **DispatherServlet 동작 순서**
1. 요청을 분석한다. (로케일, 테마, 멀티파크 등등)
2. (핸들러 매핑에게 위임하여) 요청을 처리할 핸들러를 찾는다.
3. (등록되어 있는 핸들러 어뎁터중) 해당 핸들러를 실핼 할 수 있는 "핸들러 어뎁터"를 찾는다.
4. 찾아낸 "핸들러 어뎁터"를 사용해서 핸들러의 응답을 처리한다.
    * 핸들러의 리턴값을 보고 어떻게 처리할지를 판단한다.
        * 뷰 이름에 해당하는 뷰를 찾아서 모델 데이터를 랜더링한다.
        *  `@ResponseBody`가 있다면 `Converter`를 사용해서 응답 본문을
만들고,
5. (부가적으로) 예외가 발생했다면, 예외 처리 핸들러에 요청 처리를 위임한다.
6. 최종적으로 응답을 보낸다.


<br>

### **BeanNameUrlHandlerMapping 전략 사용하는 예제**

simpleController.java (`BeanNameUrlHandlerMapping` 전략을 사용하는 방법)
```java
@org.springframework.stereotype.Controller("/simple")
public class SimpleController implements Controller {
@Override
public ModelAndView handleRequest(HttpServletRequest request,
HttpServletResponse response) throws Exception {
return new ModelAndView("/WEB-INF/simple.jsp");
}

```
위의 코드 같은 경우에는 `BeanNameUrlHandlerMapping` 를 사용하며 이를 실행시켜주는 어댑터는 `SimpleControllerHandlerAdapter`입니다. 

```java
@Configuration
@ComponentScan
public class WebConfig {
    @Bean
    public InternalResourceViewResolver viewResolver() {
    InternalResourceViewResolver viewResolver = new
    InternalResourceViewResolver(); viewResolver.setPrefix("/WEB-INF/");
    viewResolver.setSuffix(".jsp");
    return viewResolver;
    }
}
```
```java
@org.springframework.stereotype.Controller("/simple")
public class SimpleController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        return new ModelAndView("simple"); // --> /WEB-INF/~.jsp 생략 가능
}
}

```
---
## **스프링 MVC 구성 요소**

---
## **MVC 설정**
- `WebMvcConfigurationSupport.class`
- `WebMvcAutoConfiguration.class`
- 설정을 기본 설정이다