> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ์น MVC'๋ฅผ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>

# **์คํ๋ง MVC ํ์ฉ (ํธ๋ค๋ฌ ๋ฉ์๋)**

## **1. ํธ๋ค๋ฌ ๋ฉ์๋_์๊ท๋จผํธ์ ๋ฆฌํด ํ์**

**ํธ๋ค๋ฌ ๋ฉ์๋ ์๊ท๋จผํธ** : ์ฃผ๋ก ์์ฒญ ๊ทธ ์์ฒด ๋๋ ์์ฒญ์ ๋ค์ด์๋ ์ ๋ณด๋ฅผ ๋ฐ์์ค๋๋ฐ ์ฌ์ฉํฉ๋๋ค. 

|ํธ๋ค๋ฌ ๋ฉ์๋ ์๊ท๋จผํธ| ์ค๋ช |
|--|--|
|`WebRequest`<br>`NativeWebRequest` <br> `ServletRequest`<br> `HttpServletRequest`(Response)|์์ฒญ ๋๋ ์๋ต ์์ฒด์ ์ ๊ทผ ๊ฐ๋ฅํ API ์๋๋ค.|
|`InputStream`<br>`Reader`<br>`OutputStream`<br>`Writer`|์์ฒญ ๋ณธ๋ฌธ์ ์ฝ์ด์ค๊ฑฐ๋, ์๋ต ๋ณธ๋ฌธ์ ์ธ ๋ ์ฌ์ฉํ  ์ ์๋ API ์๋๋ค. |
|`PushBuilder`|์คํ๋ง 5, HTTP/2 ๋ฆฌ์์ค ํธ์ฌ์ ์ฌ์ฉํฉ๋๋ค. ๋ณด์ฌ์ฃผ๋ view ์์ ๋ฆฌ์์ค๊ฐ ์กด์ฌํ  ๋ ์์ฒญ์ ๋ฐ์์ ๋ pushBilder๋ฅผ ํตํด ์๋ฒ๊ฐ ๋ฅ๋์ ์ผ๋ก push ๋ฅผ ๋ฏธ๋ฆฌ ํ  ์ ์์ต๋๋ค. ๋ธ๋ผ์ฐ์ ๊ฐ ๋ฐ๋ก ์์ฒญ์ ํ์ง ์์๋ ๋๊ธฐ ๋๋ฌธ์ ์๋๊ฐ ์กฐ๊ธ ๋ ๋น ๋ฆ๋๋ค. |
|`HttpMethod`|์ฌ๋ฌ๊ฐ์ ์์ฒญ์ด ๋ค์ด์ค๋ ๊ฒฝ์ฐ (GET,POST,PUT,DELETE... ๋ฑ) ๊ทธ์ ๋ํ ์ ๋ณด๋ฅผ ์ป์ ์ ์์ต๋๋ค. |
|`Locale`<br>`TimeZone`<br>`ZoneId`|LocaleResolver๊ฐ ๋ถ์ํ ์์ฒญ์ Locale ์ ๋ณด๋ฅผ ์ป์ ์ ์์ต๋๋ค. |
|`@PathVariable`| URI ํํ๋ฆฟ ๋ณ์ ์ฝ์ ๋ ์ฌ์ฉํฉ๋๋ค.|
|`@MatrixVariable`| URI ๊ฒฝ๋ก ์ค์ ํค/๊ฐ ์์ ์ฝ์ด ์ฌ ๋ ์ฌ์ฉํฉ๋๋ค.|
|`@RequestParam`| ์๋ธ๋ฆฟ ์์ฒญ ๋งค๊ฐ๋ณ์ ๊ฐ์ ์ ์ธํ ๋ฉ์๋ ์๊ท๋จผํธ ํ์์ผ๋ก ๋ณํํด์ค๋๋ค.<br>๋จ์ ํ์์ธ ๊ฒฝ์ฐ์ ์ด ์ ๋ธํ์ด์์ ์๋ตํ  ์ ์์ต๋๋ค.|
|`@RequestHeader`| ์์ฒญ ํค๋ ๊ฐ์ ์ ์ธํ ๋ฉ์๋ ์๊ท๋จผํธ ํ์์ผ๋ก ๋ณํํด์ค๋๋ค.|


**ํธ๋ค๋ฌ ๋ฉ์๋ ๋ฆฌํด**: ์ฃผ๋ก ์๋ต ๋๋ ๋ชจ๋ธ์ ๋๋๋งํ  ๋ทฐ์ ๋ํ ์ ๋ณด๋ฅผ ์ ๊ณตํ๋๋ฐ ์ฌ์ฉํฉ๋๋ค. 
|||
|--|--|
|`@ResponseBody` |๋ฆฌํด ๊ฐ์ HttpMessageConverter๋ฅผ ์ฌ์ฉํด ์๋ต ๋ณธ๋ฌธ์ผ๋ก ์ฌ์ฉํฉ๋๋ค.|
|`HttpEntity`<br>`ReponseEntity`|์๋ต ๋ณธ๋ฌธ ๋ฟ ์๋๋ผ ํค๋ ์ ๋ณด๊น์ง, ์ ์ฒด ์๋ต์ ๋ง๋ค ๋ ์ฌ์ฉํฉ๋๋ค.<br>ResponseEntity๋ status ์ฝ๋์ ์๋ต header ๋ฐ ์๋ต body๋ฅผ ์ธํํ  ์ ์์ต๋๋ค. (build())๋ฅผ ์ฌ์ฉํฉ๋๋ค. |
|`String`| ViewResolver๋ฅผ ์ฌ์ฉํด์ ๋ทฐ๋ฅผ ์ฐพ์ ๋ ์ฌ์ฉํ  ๋ทฐ ์ด๋ฆ์๋๋ค. SpringBoot ๋ฅผ ์ฌ์ฉํ๋ค๋ฉด return์ผ๋ก ๋ณด๋ด์ค String ์  viewResolver ๊ฐ ๊ธฐ๋ณธ์ ์ผ๋ก ์ฐพ์ต๋๋ค. ์๋ฅผ๋ค์ด, "event"๋ผ๋ String ์ return ํ๋ค๋ฉด viewResolver๋ event.html ์ธ ํ์ผ์ ์ฐพ์์ ๋ฆฌํดํด์ค๋๋ค.  |
|`View`| ์๋ฌต์ ์ธ ๋ชจ๋ธ ์ ๋ณด๋ฅผ ๋๋๋งํ  ๋ทฐ ์ธ์คํด์ค|
|`Map`<br>`Model`|(RequestToViewNameTranslator๋ฅผ ํตํด์ ๋ทฐ์ ์ ๋ณด๋ฅผ ์ ์ถํฉ๋๋ค.) ์๋ฌต์ ์ผ๋ก ํ๋จํ ๋ทฐ ๋๋๋งํ  ๋ ์ฌ์ฉํ  ๋ชจ๋ธ ์ ๋ณด์๋๋ค.|
|`@ModelAttribute`| (RequestToViewNameTranslator๋ฅผ ํตํด์) ์๋ฌต์ ์ผ๋ก ํ๋จํ ๋ทฐ ๋๋๋งํ  ๋ ์ฌ์ฉํ  ๋ชจ๋ธ ์ ๋ณด์ ์ถ๊ฐํฉ๋๋ค.<br>์ด ์ ๋ธํ์ด์์ ์๋ตํ  ์ ์์ต๋๋ค.|


**์ฐธ๊ณ **

https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-arguments

---
## **2. ํธ๋ค๋ฌ ๋ฉ์๋_URI ํจํด**
`@PathVariable`
* ์์ฒญ URI ํจํด์ ์ผ๋ถ๋ฅผ ํธ๋ค๋ฌ ๋ฉ์๋ ์๊ท๋จผํธ๋ก ๋ฐ๋ ๋ฐฉ๋ฒ์๋๋ค.
* ํ์ ๋ณํ ์ง์ํฉ๋๋ค.
* (๊ธฐ๋ณธ)๊ฐ์ด ๋ฐ๋์ ์์ด์ผ ํฉ๋๋ค.
* Optional ์ง์์ด ๊ฐ๋ฅํฉ๋๋ค. (required = false ๋ฅผ ์ค๊ฒ๊ณผ ๋ง์ฐฌ๊ฐ์ง์๋๋ค. )
* `@PathVariable`์ ์ฌ์ฉํ  ๋ ๊ฒฝ๋ก์ด๋ฆ๊ณผ ๋ณ์์ด๋ฆ์ ๋ง์ถฐ์ค ํ์๋ ์์ต๋๋ค. ๋ค๋ง, ๋ค๋ฅธ ์ด๋ฆ์ ์ฌ์ฉํ  ๊ฒฝ์ฐ `@PathVariable("๋งตํํ  ๊ฐ ์ด๋ฆ")`์ ์์ฑํ์ฌ ์๋ ค์ค์ผ ํฉ๋๋ค. 

`@MatrixVariable`
* ์์ฒญ URI ํจํด์์ ํค/๊ฐ ์์ ๋ฐ์ดํฐ๋ฅผ ๋ฉ์๋ ์๊ท๋จผํธ๋ก ๋ฐ๋ ๋ฐฉ๋ฒ์๋๋ค. 
* ํ์ ๋ณํ ์ง์.
* (๊ธฐ๋ณธ)๊ฐ์ด ๋ฐ๋์ ์์ด์ผ ํฉ๋๋ค.
* Optional ์ง์ํฉ๋๋ค. 
* ์ด ๊ธฐ๋ฅ์ ๊ธฐ๋ณธ์ ์ผ๋ก ๋นํ์ฑํ ๋์ด ์์ผ๋ฏ๋ก ํ์ฑํ ํ๋ ค๋ฉด ๋ค์๊ณผ ๊ฐ์ด ์ค์ ํด์ผ ํฉ๋๋ค.
```java
@Configuration
public class webConfig implements WebMvcConfigurer {

    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper urlPathHelper = new UrlPathHelper();
        urlPathHelper.setRemoveSemicolonContent(false); // ์ธ๋ฏธ์ฝ๋ก ์ ์ ๊ฑฐํ์ง ์๋๋ก ์ค์  -> ๊ทธ๋์ผ @MatrixVariable ์ ์ฌ์ฉํ  ์ ์์
        configurer.setUrlPathHelper(urlPathHelper);
    }
}
```
**์์ ์ฝ๋ 1**
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

**์์ ์ฝ๋ 2**
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
## **3. ํธ๋ค๋ฌ ๋ฉ์๋_์์ฒญ ๋งค๊ฐ๋ณ์ (@RequestParam)**
`@RequestParam`
* ์์ฒญ ๋งค๊ฐ๋ณ์์ ๋ค์ด์๋ ๋จ์ ํ์ ๋ฐ์ดํฐ๋ฅผ ๋ฉ์๋ ์๊ท๋จผํธ๋ก ๋ฐ์์ฌ ์ ์์ต๋๋ค. 
* required์ ๊ธฐ๋ณธ๊ฐ์ด true ์ด๊ธฐ ๋๋ฌธ์ ๊ฐ์ด ๋ฐ๋์ ์์ด์ผ ํฉ๋๋ค.
    * required=false ๋๋ Optional์ ์ฌ์ฉํด์ ๋ถ๊ฐ์ ์ธ ๊ฐ์ผ๋ก ์ค์ ํ  ์๋ ์์ต๋๋ค. 
* String์ด ์๋ ๊ฐ๋ค์ ํ์ ์ปจ๋ฒ์ ์ ์ง์ํฉ๋๋ค.
* `Map<String, String>` ๋๋ `MultiValueMap<String, String>`์ ์ฌ์ฉํด์ ๋ชจ๋  ์์ฒญ ๋งค๊ฐ๋ณ์๋ฅผ ๋ฐ์ ์ฌ ์๋ ์๋ค.
* ์ด ์ ๋ธํ์ด์์ ์๋ต ํ  ์ ์์ต๋๋ค. (ํ์ง๋ง ์๋ตํ๋ ๊ฒ์ ๊ถ์ฅํ์ง ์์)

**์์ ์ฝ๋**
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



**์์ฒญ ๋งค๊ฐ๋ณ์๋?**
* ์ฟผ๋ฆฌ ๋งค๊ฐ๋ณ์
* ํผ ๋ฐ์ดํฐ

---
## **4. ํธ๋ค๋ฌ ๋ฉ์๋_@ModelAttrubute**
`@ModelAttrubute`
* `@RequestParam`์ ๋จ์ผ๊ฐ์ฒด๋ฅผ ๋ฐ์์ฌ ๋ ์ฌ์ฉํ๋ ๊ฒ์ด์ง๋ง, `@ModelAttribute`๋ ์ฌ๋ฌ ๊ณณ์ ์๋ ๋ฐ์ดํฐ๋ฅผ ๋ณตํฉ ํ์ ๊ฐ์ฒด๋ก ๋ฐ์์ค๊ฑฐ๋ ํด๋น ๊ฐ์ฒด๋ฅผ ์๋ก ๋ง๋ค ๋ ์ฌ์ฉํ  ์ ์์ต๋๋ค. 
* ์๋ต์ด ๊ฐ๋ฅํฉ๋๋ค. 
* ๊ฐ์ ๋ฐ์ธ๋ฉ ํ  ์ ์๋ ๊ฒฝ์ฐ์๋? ex) Integer ํ์์ธ๋ฐ String์ผ๋ก ๋๊ธฐ๋ ๊ฒฝ์ฐ
    * `BindException` ๋ฐ์ 400 ์๋ฌ
* ๋ฐ์ธ๋ฉ ์๋ฌ๋ฅผ ์ง์  ๋ค๋ฃจ๊ณ  ์ถ์ ๊ฒฝ์ฐ
    * `BindingResult` ํ์์ ์๊ท๋จผํธ๋ฅผ ๋ฐ๋ก ์ค๋ฅธ์ชฝ์ ์ถ๊ฐํฉ๋๋ค.
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
* ๋ฐ์ธ๋ฉ ์ดํ์ ๊ฒ์ฆ ์์์ ์ถ๊ฐ๋ก ํ๊ณ  ์ถ์ ๊ฒฝ์ฐ
    * `@Valid` ๋๋ `@Validated` ์ ๋ธํ์ด์์ ์ฌ์ฉํฉ๋๋ค.
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
Event ํด๋์ค์ limit ์ ์ต์ 0 ์ด์์ด์ด์ผ ํ๋ค๊ณ  `@Min`์ ์ ์ธํ๊ณ , Controller ์ฝ๋์์ `@Valid`์ด๋ธํ์ด์์ ๋ถ์์ต๋๋ค. 

๊ทธ๋ฆฌ๊ณ  limit ์ ์์๋ฅผ ์ค์ ํ ๋ค์์ ํ์คํธ ์ฝ๋๋ฅผ ์คํ์ํค๊ฒ ์ต๋๋ค. 
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

๋ฐ์ธ๋ฉ์ ์ฑ๊ณตํ์ง๋ง, ๋ค์๊ณผ ๊ฐ์ด Error ๊ฐ ์ฐํ๋ ๊ฒ์ ํ์ธํ  ์ ์์ต๋๋ค.

* `@Validated`
    * ๊ทธ๋ฃน์ ์ค์ ํ  ์ ์์ต๋๋ค. 
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
๋ค์๊ณผ ๊ฐ์ด ์ฝ๋๋ฅผ ์์ฑํ๊ณ  ์์์ ์์ฑํ๋ Test ์ฝ๋๋ฅผ ์คํ์ํค๋ฉด Validation ์ฒดํฌ๋ฅผ ํ์ง ์์ต๋๋ค. Name ์ Blank ์ฌ๋ถ๋ง ์ํด์๋ Valid ๊ทธ๋ฃน์ด๊ธฐ ๋๋ฌธ์๋๋ค. 

---
## **4. ํธ๋ค๋ฌ ๋ฉ์๋_ํผ ์๋ธ๋ฐ (์๋ฌ ์ฒ๋ฆฌ)**
* ๋ฐ์ธ๋ฉ ์๋ฌ ๋ฐ์ ์ Model์ ๋ด๊ธฐ๋ ์ ๋ณด
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
* ํ์๋ฆฌํ ์ฌ์ฉ์ ๋ฐ์ธ๋ฉ ์๋ฌ ๋ณด์ฌ์ฃผ๊ธฐ
    - https://www.thymeleaf.org/doc/tutorials/2.1/thymeleafspring.html#field-errors

![image](https://user-images.githubusercontent.com/63777714/144965421-967e9e3e-beb1-4524-ad9a-661c4d32475c.png)
![image](https://user-images.githubusercontent.com/63777714/144965464-ae249098-6e15-417c-952c-00ed5949e8ea.png)
        
> ```java
> <p th:if="${#fields.hasErrors('limit')}" th:errors="*{limit}">Incorrect date</p>
> ```
* ํ์๋ฆฌํ ๋ชฉ๋ก ๋ณด์ฌ์ฃผ๊ธฐ
    * https://www.thymeleaf.org/doc/tutorials/2.1/thymeleafspring.html#listing-seed-starter-data
```java
<a th:href="@{/events/form}">Create New Event</a>
<div th:unless="${#lists.isEmpty(eventList)}">
    <ul th:each="event: ${eventList}">
        <p th:text="${event.Name}">Event Name</p>
    </ul>
</div>
```

* Post / Redirect / Get ํจํด
    * https://en.wikipedia.org/wiki/Post/Redirect/Get
    * Post ์ดํ์ ๋ธ๋ผ์ฐ์ ๋ฅผ ๋ฆฌํ๋์ ํ๋๋ผ๋ ํผ ์๋ธ๋ฐ์ด ๋ฐ์ํ์ง ์๋๋ก redirect๋ฅผ ์ฌ์ฉํ๋ ํจํด
    * Post ์ดํ์ ๋์ผํ url์ Get ์์ฒญ์ผ๋ก ๋ณด๋ด์ ํผ์๋ธ๋ฐ์ด ์ค๋ณต ๋ฐ์ํ์ง ์๋๋ก ํฉ๋๋ค. 
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
## **5. ํธ๋ค๋ฌ ๋ฉ์๋_@SessionAttributes**

๋ชจ๋ธ ์ ๋ณด๋ฅผ HTTP ์ธ์์ ์ ์ฅํด์ฃผ๋ ์ ๋ธํ์ด์
* HttpSession์ ์ง์  ์ฌ์ฉํ  ์๋ ์์ง๋ง ์ด ์ ๋ธํ์ด์์ ์ค์ ํ ์ด๋ฆ์ ํด๋นํ๋ ๋ชจ๋ธ ์ ๋ณด๋ฅผ ์๋์ผ๋ก ์ธ์์
๋ฃ์ด์ค๋ค. 
* @ModelAttribute๋ ์ธ์์ ์๋ ๋ฐ์ดํฐ๋ ๋ฐ์ธ๋ฉํ๋ค.
* ์ฌ๋ฌ ํ๋ฉด(๋๋ ์์ฒญ)์์ ์ฌ์ฉํด์ผ ํ๋ ๊ฐ์ฒด๋ฅผ ๊ณต์ ํ  ๋ ์ฌ์ฉํ๋ค.
```java
@GetMapping("/events/form")
public String eventsForm(Model model, HttpSession httpSesseion) {
    Event newEvent = new Event();
    model.addAttribute("event", newEvent);
    httpSesseion.setAttribute("event",newEvent);
    return "/events/form";
}

// ๋๋
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

* session์ ์ฌ์ฉํ๋ ์ด์  (์์ )
    * ์ฅ๋ฐ๊ตฌ๋์ ๋ฐ์ดํฐ ์ ์ง
    * ์ด๋ค ๋ฐ์ดํฐ๋ฅผ ์์ฑํ  ๋ ์ฌ๋ฌ ํ๋ฉด์ ๊ฑธ์ณ์ ๋ง๋ค์ด์ผ ํ๋ ๊ฒฝ์ฐ์ ๋ฐ์ดํฐ ์ ์ง
    *  

SessionStatus๋ฅผ ์ฌ์ฉํด์ ์ธ์ ์ฒ๋ฆฌ ์๋ฃ๋ฅผ ์๋ ค์ค ์ ์์ต๋๋ค.
* ํผ ์ฒ๋ฆฌ ๋๋๊ณ  ์ธ์์ ๋น์ธ ๋ ์ฌ์ฉํฉ๋๋ค.
```java
sessionStatus.setComplete();
```

---

## **6. ํธ๋ค๋ฌ ๋ฉ์๋_@SessionAttribute**
* HTTP ์ธ์์ ๋ค์ด์๋ ๊ฐ ์ฐธ์กฐํ  ๋ ์ฌ์ฉํฉ๋๋ค. (`@SessionAttributes` ๋์ ๋ค๋ฅธ๊ฒ!)
* HttpSession์ ์ฌ์ฉํ  ๋ ๋นํด ํ์ ์ปจ๋ฒ์ ์ ์๋์ผ๋ก ์ง์ํ๊ธฐ ๋๋ฌธ์ ํธ๋ฆฌํฉ๋๋ค. 
* HTTP ์ธ์์ ๋ฐ์ดํฐ๋ฅผ ๋ฃ๊ณ  ๋นผ๊ณ  ์ถ์ ๊ฒฝ์ฐ์๋ HttpSession์ ์ฌ์ฉํ  ๊ฒ์ ๊ถ์ฅํฉ๋๋ค. 
* `@SessionAttributes`๋ ํด๋น ์ปจํธ๋กค๋ฌ ๋ด์์๋ง ๋์ํฉ๋๋ค. 
    * ์ฆ, ํด๋น ์ปจํธ๋กค๋ฌ ์์์ ๋ค๋ฃจ๋ ํน์  ๋ชจ๋ธ ๊ฐ์ฒด๋ฅผ ์ธ์์ ๋ฃ๊ณ  ๊ณต์ ํ  ๋ ์ฌ์ฉ. 
* `@SessionAttribute`๋ ์ปจํธ๋กค๋ฌ ๋ฐ(์ธํฐ์ํฐ ๋๋ ํํฐ ๋ฑ)์์ ๋ง๋ค์ด ์ค ์ธ์ ๋ฐ์ดํฐ์
์ ๊ทผํ  ๋ ์ฌ์ฉํฉ๋๋ค. 

|@SessionAttributes|@SessionAttribute|
|--|--|
| HTTP ์ธ์์ ๋ค์ด์๋ ๊ฐ์ ์ฐธ์กฐํ  ๋ ์ฌ์ฉํจ| HTTP ์ธ์์ ๋ค์ด์๋ ๊ฐ์ ์ฐธ์กฐํ  ๋ ์ฌ์ฉํจ|
| ํด๋น ์ปจํธ๋กค๋ฌ ๋ด์์๋ง ๋์ |ํด๋น ์ปจํธ๋กค๋ฌ ๋ฐ(์ธํฐ์ํฐ ๋๋ ํํฐ ๋ฑ)์์๋ ๋์|
|ํด๋น ์ปจํธ๋กค๋ฌ ์์์ ๋ค๋ฃจ๋ ํน์  ๋ชจ๋ธ ๊ฐ์ฒด๋ฅผ ์ธ์์ ๋ฃ๊ณ  ๊ณต์ ํ  ๋ ์ฌ์ฉ| ํด๋น ์ปจํธ๋กค๋ฌ ๋ฐ(์ธํฐ์ํฐ ๋๋ ํํฐ ๋ฑ)์์๋ง๋ค์ด ์ค ์ธ์ ๋ฐ์ดํฐ์ ์ ๊ทผํ  ๋ ์ฌ์ฉ|

**@SessionAttribute ์์ **
```java
public class VisitTimeInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
        Object handler) throws Exception {
        HttpSession session = request.getSession();
        if (session.getAttribute("visitTime") == null) {
            session.setAttribute("visitTime", LocalDateTime.now());
        }
        return true;
    }
}
```
```java
@Configuration
public class webConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new VisitTimeInterceptor());
    }
}
```
```java
    @GetMapping("/events/list")
    public String getEvent(Model model, @SessionAttribute LocalDateTime visitTime) {
        System.out.println(visitTime);

        return "/events/list";
    }
}
```
![image](https://user-images.githubusercontent.com/63777714/144972045-ff37187b-0c42-4c77-ad34-bf6df4635b09.png)
