> 📚 본 글은 인프런 강의 '스프링 웹 MVC'를 듣고 정리한 글입니다. 

<br>

# **스프링 MVC 활용 (핸들러 메소드)**

## **7. 핸들러 메소드_RedirectAttributes**
리다이렉트 할 때 기본적으로 Model에 들어있는 primitive type 데이터는 URI 쿼리 매개변수에
추가됩니다.
* Spring MVC 에서는 이 기능이 기본적으로 활성화 되어 있습니다. 
* 스프링 부트에서는 이 기능이 기본적으로 비활성화 되어 있습니다.
* Ignore-default-model-on-redirect 프로퍼티를 사용해서 활성화 할 수 있습니다.

원하는 값만 리다이렉트 할 때 전달하고 싶다면 RedirectAttributes에 명시적으로 추가할 수 있습니다.

리다이렉트 요청을 처리하는 곳에서 쿼리 매개변수를 `@RequestParam` 또는 `@ModelAttribute`로
받을 수 있습니다.

### **예제코드**
```java
// Controller
@PostMapping("/events/form/limit")
public String eventsFormLimitSubmit(@Valid @ModelAttribute Event event,
    BindingResult bindingResult,
    SessionStatus sessionStatus,
    Model model) {
    if (bindingResult.hasErrors()) {
        return "/events/form-name";
    }
    sessionStatus.setComplete();
    model.addAttribute("name", event.getName());
    model.addAttribute("limit", event.getLimit());
    return "redirect:/events/list";
}
```
```java
// application.properties
spring.mvc.ignore-default-model-on-redirect=false
```
![image](https://user-images.githubusercontent.com/63777714/144978273-401a95c1-1ad0-4b53-a200-e395b8e90c25.png)

저는 스프링 부트 환경에서 프로젝트를 실행했기 때문에 application.properties 에서 redirect 설정값을 false 로 변경해주고 실행하였습니다. name과 limit 을 입력한 후 redirect:\events\list 로 이동하니 파라미터 값에 위 사진처럼 입력한 값이 표시된 것을 확인할 수 있었습니다. 

만약 파라미터로 보낸 name과 limit 를 모두 보내고 싶은 게 아니라 일부만 보내고 싶다면 `RedirectAttributes` 이용하여 다음과 같이 코드를 작성하면 됩니다. 
```java
@PostMapping("/events/form/limit")
public String eventsFormLimitSubmit(@Valid @ModelAttribute Event event,
    BindingResult bindingResult,
    SessionStatus sessionStatus,
    RedirectAttributes redirectAttributes) {
    if (bindingResult.hasErrors()) {
        return "/events/form-name";
    }
    sessionStatus.setComplete();
    redirectAttributes.addAttribute("name", event.getName());
    return "redirect:/events/list";
}
```
application.properties 파일은 스프링부터 기본 설정으로 놔두시면 됩니다. 

이렇게 작성할 시 RedirectAttributes에 addAttribute를 통해 명시한 것들만 쿼리 파라미터를 사용해서 리다이렉트 하는 URL 쪽으로 데이터가 전달이 됩니다.

![image](https://user-images.githubusercontent.com/63777714/144978936-59569f23-2d88-4f98-bbdb-99c75df59867.png)

이를 받는쪽에선 `@RequestParam` 또는 `@ModelAttribute`를 통해서 받아서 사용하면 됩니다. 
> 💡 주의 <br>
> `@ModelAttrubute`를 사용하여 가져올 경우엔 `@SessionAttribute` 에서 사용한 이름과 동일한 이름을 사용하면 안됩니다. <br>
> `@ModelAttrubute`을 사용할 경우 Session에서 우선적으로 가져오려고 노력하며, setComplate 를 한 경우엔 session이 존재하지 않기 때문에 에러가 날 수 있습니다. 

---
## **8. 핸들러 메소드_Flash Attributes**
주로 리다이렉트시에 데이터를 전달할 때 사용합니다. 
* 데이터가 URI에 노출되지 않습니다. (🤷‍♂️ ? HttpSession을 통해 데이터가 전달되기 때문에)
* 임의의 객체를 저장할 수 있다.
* 보통 HTTP 세션을 사용한다.

리다이렉트 하기 전에 데이터를 HTTP 세션에 저장하고 리다이렉트 요청을 처리 한 다음 그 즉시 제거합니다. 
RedirectAttributes를 통해 사용할 수 있습니다. 

### **예제코드**
```java
@PostMapping("/events/form/limit")
public String eventsFormLimitSubmit(@Valid @ModelAttribute Event event,
                                BindingResult bindingResult,
                                SessionStatus sessionStatus,
                                RedirectAttributes redirectAttributes) {
    if (bindingResult.hasErrors()) {
        return "/events/form-name";
    }
    sessionStatus.setComplete();
    // RedirectAttributes를 통해 FlashAttribute 를 사용 
    redirectAttributes.addFlashAttribute("newEvent", event); // newEvent 라는 이름으로 HttpSession 에 저장이 되고, 리다이렉트 요청을 처리하고 즉시 제거됨
    return "redirect:/events/list";
}
```

> 🕵🏻‍♂️ `FlashAttributes`와 `RedirectAttributes`의 가장 큰 차이 ? 
> * `RedirectAttributes`
>   * URI의 경로에 쿼리 파라미터로 데이터가 붙는 것입니다. 
>   * URI 경로에 붙기 때문에 반드시 String으로 변환이 가능한 값이어야 합니다. (Event와 같은 Entity를 넣어줄 순 없습니다. (Converter 가 등록되면 가능))
> * `FlashAttributes`
>   * HttpSession 을 통해 데이터를 넘겨주기 때문에 URI에 데이터가 노출되지 않습니다. 
>   * Converter가 등록되어 있지 않아도 Entity 객체를 받을 수 있습니다. (Session에는 Object로 등록이 가능)

---
## **9. 핸들러 메소드_MultipartFile**
### **MultipartFile**
* 파일 업로드시 사용하는 메소드 아규먼트
* MultipartResolver 빈이 설정 되어 있어야 사용할 수 있다. (스프링 부트 자동 설정이 해 줌) 
* POST multipart/form-data 요청에 들어있는 파일을 참조할 수 있습니다. 
* List<MultipartFile> 아큐먼트로 여러 파일을 참조할 수도 있습니다. 

### **파일 업로드 관련 스프링 부트 설정**
* MultipartAutoConfiguration
* MultipartProperties

### **예제코드**
```java
@PostMapping("/file")
    public String uploadFile(@RequestParam MultipartFile file,
        RedirectAttributes attributes) {
        String message = file.getOriginalFilename() + " is uploaded.";
        System.out.println(message);
        attributes.addFlashAttribute("message", message);
        return "redirect:/events/list";
    }
``` 
```java
// 테스트 코드
@Test
public void fileUploadTest() throws Exception {
    // 가짜 File 객체
    MockMultipartFile file = new MockMultipartFile(
        "file",
        "test.txt",
        "text/plain",
        "hello file".getBytes());

    this.mockMvc.perform(multipart("/file").file(file))
        .andDo(print())
        .andExpect(status().is3xxRedirection());
}
```

--- 
## **10. 핸들러 메소드_파일 다운로드 및 ResponseEntity**
### **파일 리소스를 읽어오는 방법**
* 스프링 ResourceLoader 사용하기 

### **파일 다운로드 응답 헤더에 설정할 내용**
* Content-Disposition: 사용자가 해당 파일을 받을 때 사용할 파일 이름
* Content-Type: 어떤 파일인가
* Content-Length: 얼마나 큰 파일인가

### **파일의 종류(미디어타입)을 알아내는 방법**
*  http://tika.apache.org/
* 아래의 의존성 추가
```xml
<!-- https://mvnrepository.com/artifact/org.apache.tika/tika-core -->
<dependency>
    <groupId>org.apache.tika</groupId>
    <artifactId>tika-core</artifactId>
    <version>2.1.0</version>
</dependency>
```

### **ResponseEntity**
* 응답 상태 코드
* 응답 헤더
* 응답 본문

### **예제코드**
```java
 @GetMapping("/file/{filename}")
public ResponseEntity<Resource> fileDownload(@PathVariable String filename) throws IOException {
    Resource resource = resourceLoader.getResource("classpath:" + filename);
    File file = resource.getFile();

    Tika tika = new Tika();
    String mediaType = tika.detect(file);

    return ResponseEntity.ok()
        .header(HttpHeaders.CONTENT_DISPOSITION,
            "attachement; filename=\"" + resource.getFilename() + "\"")
        .header(HttpHeaders.CONTENT_TYPE, mediaType)
        .header(HttpHeaders.CONTENT_LENGTH, file.length() + "")
        .body(resource);

}
```
http://localhost:8080/file/test.png URL 입력시 다음과 같이 다운로드 받는 화면 나타납니다. 

![image](https://user-images.githubusercontent.com/63777714/144994718-b6247661-9c54-49eb-8630-9a81aabc6cad.png)

**참고**
* https://spring.io/guides/gs/uploading-files/
* https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition
* https://www.baeldung.com/java-file-mime-type

---
## **11. 핸들러 메소드_@RequestBody & HttpEntity** 

### **@RequestBody**
* 요청 본문(body)에 들어있는 데이터를 `HttpMessageConveter`를 통해 변환한 객체로
받아올 수 있습니다.
* `@Valid` 또는 `@Validated`를 사용해서 값을 검증 할 수 있습니다.
* `BindingResult` 아규먼트를 사용해 코드로 바인딩 또는 검증 에러를 확인할 수 있습니다.
```java
@RestController
@RequestMapping("/api/events")
public class EventApi {

    @PostMapping
    public Event createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
        // save event
        if (bindingResult.hasErrors()) {
            bindingResult.getAllErrors().forEach(error -> {
                System.out.println(error);
            });
        }
        return event;
    }
}
```

### **HttpMessageConverter**
* 스프링 MVC 설정 (WebMvcConfigurer)에서 설정할 수 있다.
* `configureMessageConverters`: 기본 메시지 컨버터 대체
* `extendMessageConverters`: 메시지 컨버터에 추가
* 기본 컨버터
    * WebMvcConfigurationSupport.addDefaultHttpMessageConverters

### **HttpEntity**
* 요청 본문(body)에 들어있는 데이터를 `HttpMessageConveter`를 통해 변환한 객체로
받아올 수 있습니다.
* @RequestBody와 비슷하지만 추가적으로 요청 헤더 정보를 사용할 수 있습니다.
```java
@RestController
@RequestMapping("/api/events")
public class EventApi {

    @PostMapping
    public Event createEvent(HttpEntity<Event> request) {
        // save event
        MediaType contentType = request.getHeaders().getContentType();
        System.out.println(contentType);
        return request.getBody();
    }
}
```
> 핸들러 어댑터가 `@RequestBody` 로 들어온 Method Arguument 를 resolve 할 때 핸들러 어댑터에 등록되어 있는 `HttpMessageConverter`를 사용을 해서 Convert를 진행합니다. 

---
## **12. 핸들러 메소드_@ResponseBody & ResponseEntity** 
### **@ResponseBody**
* 데이터를 `HttpMessageConverter`를 사용해 응답 본문 메시지로 보낼 때 사용합니다.
* `@RestController` 사용시 자동으로 모든 핸들러 메소드에 적용됩니다.
### **ResponseEntity**
* 응답 헤더 상태 코드 본문을 직접 다루고 싶은 경우에 사용합니다.

```java
@PostMapping
public ResponseEntity<Event> createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
    // save event
    if (bindingResult.hasErrors()) {
        return ResponseEntity.badRequest().build();
    }
    return ResponseEntity.ok(event);
}
```