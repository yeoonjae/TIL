> 📚 본 글은 인프런 강의 '스프링 웹 MVC'를 듣고 정리한 글입니다. 

<br>

# **스프링 MVC 활용 (핸들러 메소드)**

## **1. 핸들러 메소드_아규먼트와 리턴 타입**

**핸들러 메소드 아규먼트** : 주로 요청 그 자체 또는 요청에 들어있는 정보를 받아오는데 사용합니다. 

|핸들러 메소드 아규먼트| 설명 |
|--|--|
|`WebRequest`<br>`NativeWebRequest` <br> `ServletRequest`<br> `HttpServletRequest`(Response)|요청 또는 응답 자체에 접근 가능한 API 입니다.|
|`InputStream`<br>`Reader`<br>`OutputStream`<br>`Writer`|요청 본문을 읽어오거나, 응답 본문을 쓸 때 사용할 수 있는 API 입니다. |
|`PushBuilder`|스프링 5, HTTP/2 리소스 푸쉬에 사용합니다. 보여주는 view 에서 리소스가 존재할 때 요청을 받았을 때 pushBilder를 통해 서버가 능동적으로 push 를 미리 할 수 있습니다. 브라우저가 따로 요청을 하지 않아도 되기 때문에 속도가 조금 더 빠릅니다. |
|`HttpMethod`|여러개의 요청이 들어오는 경우 (GET,POST,PUT,DELETE... 등) 그에 대한 정보를 얻을 수 있습니다. |
|`Locale`<br>`TimeZone`<br>`ZoneId`|LocaleResolver가 분석한 요청의 Locale 정보를 얻을 수 있습니다. |
|`@PathVariable`| URI 템플릿 변수 읽을 때 사용합니다.|
|`@MatrixVariable`| URI 경로 중에 키/값 쌍을 읽어 올 때 사용합니다.|
|`@RequestParam`| 서블릿 요청 매개변수 값을 선언한 메소드 아규먼트 타입으로 변환해줍니다.<br>단순 타입인 경우에 이 애노테이션을 생략할 수 있습니다.|
|`@RequestHeader`| 요청 헤더 값을 선언한 메소드 아규먼트 타입으로 변환해줍니다.|


**핸들러 메소드 리턴**: 주로 응답 또는 모델을 랜더링할 뷰에 대한 정보를 제공하는데 사용합니다. 
|||
|--|--|
|`@ResponseBody` |리턴 값을 HttpMessageConverter를 사용해 응답 본문으로 사용합니다.|
|`HttpEntity`<br>`ReponseEntity`|응답 본문 뿐 아니라 헤더 정보까지, 전체 응답을 만들 때 사용합니다.<br>ResponseEntity는 status 코드와 응답 header 및 응답 body를 세팅할 수 있습니다. (build())를 사용합니다. |
|`String`| ViewResolver를 사용해서 뷰를 찾을 때 사용할 뷰 이름입니다. SpringBoot 를 사용한다면 return으로 보내준 String 을  viewResolver 가 기본적으로 찾습니다. 예를들어, "event"라는 String 을 return 한다면 viewResolver는 event.html 인 파일을 찾아서 리턴해줍니다.  |
|`View`| 암묵적인 모델 정보를 랜더링할 뷰 인스턴스|
|`Map`<br>`Model`|(RequestToViewNameTranslator를 통해서 뷰의 정보를 유추합니다.) 암묵적으로 판단한 뷰 랜더링할 때 사용할 모델 정보입니다.|
|`@ModelAttribute`| (RequestToViewNameTranslator를 통해서) 암묵적으로 판단한 뷰 랜더링할 때 사용할 모델 정보에 추가합니다.<br>이 애노테이션은 생략할 수 있습니다.|


**참고**

https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-arguments

---
## **2. 핸들러 메소드_URI 패턴**
`@PathVariable`
* 요청 URI 패턴의 일부를 핸들러 메소드 아규먼트로 받는 방법입니다.
* 타입 변환 지원합니다.
* (기본)값이 반드시 있어야 합니다.
* Optional 지원이 가능합니다. (required = false 를 준것과 마찬가지입니다. )
* `@PathVariable`을 사용할 때 경로이름과 변수이름을 맞춰줄 필요는 없습니다. 다만, 다른 이름을 사용할 경우 `@PathVariable("맵핑할 값 이름")`을 작성하여 알려줘야 합니다. 

`@MatrixVariable`
* 요청 URI 패턴에서 키/값 쌍의 데이터를 메소드 아규먼트로 받는 방법입니다. 
* 타입 변환 지원.
* (기본)값이 반드시 있어야 합니다.
* Optional 지원합니다. 
* 이 기능은 기본적으로 비활성화 되어 있으므로 활성화 하려면 다음과 같이 설정해야 합니다.
```java
@Configuration
public class webConfig implements WebMvcConfigurer {

    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper urlPathHelper = new UrlPathHelper();
        urlPathHelper.setRemoveSemicolonContent(false); // 세미콜론을 제거하지 않도록 설정 -> 그래야 @MatrixVariable 을 사용할 수 있음
        configurer.setUrlPathHelper(urlPathHelper);
    }
}
```
**예제코드 1**
```java
@Controller
public class SampleController {

    @ResponseBody
    @GetMapping("/events/{id}")
    public Event event(@PathVariable Integer id, @MatrixVariable String name) {
        Event event = new Event();
        event.setId(id);
        event.setName(name);
        return event;
    }

}
```
```java
@ExtendWith(MockitoExtension.class)
@WebMvcTest
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void eventTest1() throws Exception {
        mockMvc.perform(get("/events/1;name=yeonjae"))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(jsonPath("id").value("1"));
    }

}
```
![image](https://user-images.githubusercontent.com/63777714/144742672-e42524c7-0ef9-486b-b2cd-ca49fa3865f3.png)

**예제코드 2**
```java
// GET /owners/42;q=11/pets/21;q=22

@GetMapping("/owners/{ownerId}/pets/{petId}")
public void findPet(
        @MatrixVariable(name="q", pathVar="ownerId") int q1,
        @MatrixVariable(name="q", pathVar="petId") int q2) {

    // q1 == 11
    // q2 == 22
}
```

--- 
## **3. 핸들러 메소드_요청 매개변수 (@RequestParam)**
`@RequestParam`
* 요청 매개변수에 들어있는 단순 타입 데이터를 메소드 아규먼트로 받아올 수 있습니다. 
* required의 기본값이 true 이기 때문에 값이 반드시 있어야 합니다.
    * required=false 또는 Optional을 사용해서 부가적인 값으로 설정할 수도 있습니다. 
* String이 아닌 값들은 타입 컨버전을 지원합니다.
* `Map<String, String>` 또는 `MultiValueMap<String, String>`에 사용해서 모든 요청 매개변수를 받아 올 수도 있다.
* 이 애노테이션은 생략 할 수 있습니다. (하지만 생략하는 것을 권장하지 않음)

**예제코드**
```java
@Controller
public class SampleController {

    @ResponseBody
    @PostMapping("/events")
    public Event event(@RequestParam String name) {
        Event event = new Event();
        event.setName(name);
        return event;
    }
}
```
```java
@ExtendWith(MockitoExtension.class)
@WebMvcTest
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void eventTest1() throws Exception {
        mockMvc.perform(post("/events")
                .param("name","yeonjae"))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("yeonjae"));
    }
}
```
![image](https://user-images.githubusercontent.com/63777714/144743356-e28fa348-e31e-4032-ae76-b83f963a0d97.png)



**요청 매개변수란?**
* 쿼리 매개변수
* 폼 데이터

---
## **4. 핸들러 메소드_@ModelAttrubute**
`@ModelAttrubute`
* `@RequestParam`은 단일객체를 받아올 때 사용하는 것이지만, `@ModelAttribute`는 여러 곳에 있는 데이터를 복합 타입 객체로 받아오거나 해당 객체를 새로 만들 때 사용할 수 있습니다. 
* 생략이 가능합니다. 
* 값을 바인딩 할 수 없는 경우에는? ex) Integer 타입인데 String으로 넘기는 경우
    * `BindException` 발생 400 에러
* 바인딩 에러를 직접 다루고 싶은 경우
    * `BindingResult` 타입의 아규먼트를 바로 오른쪽에 추가합니다.
```java
@PostMapping("/events/name/{name}")
@ResponseBody
public Event event(@ModelAttribute Event event, BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        System.out.println("====================================");
        bindingResult.getAllErrors().forEach(c -> {
            System.out.println(c.toString());
        });
    }
    return event;
}
```
* 바인딩 이후에 검증 작업을 추가로 하고 싶은 경우
    * `@Valid` 또는 `@Validated` 애노테이션을 사용합니다.
```java
@Min(0)
    private Integer limit;
```
```java
@PostMapping("/events/name/{name}")
@ResponseBody
public Event event(@Valid @ModelAttribute Event event, BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        System.out.println("====================================");
        bindingResult.getAllErrors().forEach(c -> {
            System.out.println(c.toString());
        });
    }
    return event;
}
```
Event 클래스에 limit 에 최소 0 이상이어야 한다고 `@Min`을 선언하고, Controller 코드에서 `@Valid`어노테이션을 붙였습니다. 

그리고 limit 에 음수를 설정한 다음의 테스트 코드를 실행시키겠습니다. 
```java
@Test
public void eventTest1() throws Exception {
    mockMvc.perform(post("/events/name/yeonjae")
            .param("limit","-2"))
        .andDo(print())
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.name").value("yeonjae"));

}
```

![image](https://user-images.githubusercontent.com/63777714/144960028-2d333dc1-d024-41d7-a515-5f3e59e32e98.png)

바인딩은 성공했지만, 다음과 같이 Error 가 찍히는 것을 확인할 수 있습니다.

* `@Validated`
    * 그룹을 설정할 수 있습니다. 
```java
public class Event {

    interface ValidateLimit {}
    interface ValidateName {}

    @NotBlank(groups = ValidateName.class)
    private String name;

    @Min(value = 0, groups = ValidateLimit.class)
    private Integer limit;
}
```
```java
@PostMapping("/events/name/{name}")
@ResponseBody
public Event event(@Validated(Event.ValidateName.class) @ModelAttribute Event event, BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        System.out.println("====================================");
        bindingResult.getAllErrors().forEach(c -> {
            System.out.println(c.toString());
        });
    }
    return event;
}
```
다음과 같이 코드를 작성하고 위에서 작성했던 Test 코드를 실행시키면 Validation 체크를 하지 않습니다. Name 의 Blank 여부만 속해있는 Valid 그룹이기 때문입니다. 

---
## **4. 핸들러 메소드_폼 서브밋 (에러 처리)**
* 바인딩 에러 발생 시 Model에 담기는 정보
    * Event
    * BindingResult.event
```java
@PostMapping("/events")
public String event(@Valid @ModelAttribute Event event, BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        return "/events/form";
    }
    return "/events/list";
}
```
* 타임리프 사용시 바인딩 에러 보여주기
    - https://www.thymeleaf.org/doc/tutorials/2.1/thymeleafspring.html#field-errors

![image](https://user-images.githubusercontent.com/63777714/144965421-967e9e3e-beb1-4524-ad9a-661c4d32475c.png)
![image](https://user-images.githubusercontent.com/63777714/144965464-ae249098-6e15-417c-952c-00ed5949e8ea.png)
        
> ```java
> <p th:if="${#fields.hasErrors('limit')}" th:errors="*{limit}">Incorrect date</p>
> ```
* 타임리프 목록 보여주기
    * https://www.thymeleaf.org/doc/tutorials/2.1/thymeleafspring.html#listing-seed-starter-data
```java
<a th:href="@{/events/form}">Create New Event</a>
<div th:unless="${#lists.isEmpty(eventList)}">
    <ul th:each="event: ${eventList}">
        <p th:text="${event.Name}">Event Name</p>
    </ul>
</div>
```

* Post / Redirect / Get 패턴
    * https://en.wikipedia.org/wiki/Post/Redirect/Get
    * Post 이후에 브라우저를 리프래시 하더라도 폼 서브밋이 발생하지 않도록 redirect를 사용하는 패턴
    * Post 이후에 동일한 url의 Get 요청으로 보내서 폼서브밋이 중복 발생하지 않도록 합니다. 
```java
@Controller
public class SampleController {

    @GetMapping("/events/form")
    public String eventsForm(Model model) {
        Event newEvent = new Event();
        model.addAttribute("event", newEvent);
        return "/events/form";
    }

    @PostMapping("/events")
    public String CreateEvent(@Valid @ModelAttribute Event event, BindingResult bindingResult, Model model) {
        if (bindingResult.hasErrors()) {
            return "/events/form";
        }
        List<Event> eventList = new ArrayList<>();
        eventList.add(event);
        model.addAttribute("eventList", eventList);
        return "redirect:/events/list";
    }

    @GetMapping("/events/list")
    public String getEvent(Model model) {
        Event event = new Event();
        event.setName("spring");
        event.setLimit(10);

        List<Event> eventList = new ArrayList<>();
        eventList.add(event);

        model.addAttribute(eventList);

        return "/events/list";
    }
}
```

---
## **5. 핸들러 메소드_@SessionAttributes**

모델 정보를 HTTP 세션에 저장해주는 애노테이션
* HttpSession을 직접 사용할 수도 있지만 이 애노테이션에 설정한 이름에 해당하는 모델 정보를 자동으로 세션에
넣어준다. 
* @ModelAttribute는 세션에 있는 데이터도 바인딩한다.
* 여러 화면(또는 요청)에서 사용해야 하는 객체를 공유할 때 사용한다.
```java
@GetMapping("/events/form")
public String eventsForm(Model model, HttpSession httpSesseion) {
    Event newEvent = new Event();
    model.addAttribute("event", newEvent);
    httpSesseion.setAttribute("event",newEvent);
    return "/events/form";
}

// 또는
@GetMapping("/events/form")
public String eventsForm(Model model, HttpSession httpSesseion) {
    Event newEvent = new Event();
    model.addAttribute("event", newEvent);
    return "/events/form";
}
```
```java
@Test
public void eventForm() throws Exception {
    MockHttpServletRequest request =  mockMvc.perform(get("/events/form"))
        .andDo(print())
        .andExpect(view().name("/events/form"))
        .andExpect(model().attributeExists("event"))
        .andReturn().getRequest();

    Object event = request.getSession().getAttribute("event");
    System.out.println(event);
}

```
![image](https://user-images.githubusercontent.com/63777714/144966270-28c123ec-3cce-491c-8e8c-5b357f19fa92.png)

* session을 사용하는 이유 (예제)
    * 장바구니의 데이터 유지
    * 어떤 데이터를 생성할 때 여러 화면에 걸쳐서 만들어야 하는 경우의 데이터 유지
    *  

SessionStatus를 사용해서 세션 처리 완료를 알려줄 수 있습니다.
* 폼 처리 끝나고 세션을 비울 때 사용합니다.
```java
sessionStatus.setComplete();
```
