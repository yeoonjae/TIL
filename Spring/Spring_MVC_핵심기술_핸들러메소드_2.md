> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ์น MVC'๋ฅผ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>

# **์คํ๋ง MVC ํ์ฉ (ํธ๋ค๋ฌ ๋ฉ์๋)**

## **7. ํธ๋ค๋ฌ ๋ฉ์๋_RedirectAttributes**
๋ฆฌ๋ค์ด๋ ํธ ํ  ๋ ๊ธฐ๋ณธ์ ์ผ๋ก Model์ ๋ค์ด์๋ primitive type ๋ฐ์ดํฐ๋ URI ์ฟผ๋ฆฌ ๋งค๊ฐ๋ณ์์
์ถ๊ฐ๋ฉ๋๋ค.
* Spring MVC ์์๋ ์ด ๊ธฐ๋ฅ์ด ๊ธฐ๋ณธ์ ์ผ๋ก ํ์ฑํ ๋์ด ์์ต๋๋ค. 
* ์คํ๋ง ๋ถํธ์์๋ ์ด ๊ธฐ๋ฅ์ด ๊ธฐ๋ณธ์ ์ผ๋ก ๋นํ์ฑํ ๋์ด ์์ต๋๋ค.
* Ignore-default-model-on-redirect ํ๋กํผํฐ๋ฅผ ์ฌ์ฉํด์ ํ์ฑํ ํ  ์ ์์ต๋๋ค.

์ํ๋ ๊ฐ๋ง ๋ฆฌ๋ค์ด๋ ํธ ํ  ๋ ์ ๋ฌํ๊ณ  ์ถ๋ค๋ฉด RedirectAttributes์ ๋ช์์ ์ผ๋ก ์ถ๊ฐํ  ์ ์์ต๋๋ค.

๋ฆฌ๋ค์ด๋ ํธ ์์ฒญ์ ์ฒ๋ฆฌํ๋ ๊ณณ์์ ์ฟผ๋ฆฌ ๋งค๊ฐ๋ณ์๋ฅผ `@RequestParam` ๋๋ `@ModelAttribute`๋ก
๋ฐ์ ์ ์์ต๋๋ค.

### **์์ ์ฝ๋**
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

์ ๋ ์คํ๋ง ๋ถํธ ํ๊ฒฝ์์ ํ๋ก์ ํธ๋ฅผ ์คํํ๊ธฐ ๋๋ฌธ์ application.properties ์์ redirect ์ค์ ๊ฐ์ false ๋ก ๋ณ๊ฒฝํด์ฃผ๊ณ  ์คํํ์์ต๋๋ค. name๊ณผ limit ์ ์๋ ฅํ ํ redirect:\events\list ๋ก ์ด๋ํ๋ ํ๋ผ๋ฏธํฐ ๊ฐ์ ์ ์ฌ์ง์ฒ๋ผ ์๋ ฅํ ๊ฐ์ด ํ์๋ ๊ฒ์ ํ์ธํ  ์ ์์์ต๋๋ค. 

๋ง์ฝ ํ๋ผ๋ฏธํฐ๋ก ๋ณด๋ธ name๊ณผ limit ๋ฅผ ๋ชจ๋ ๋ณด๋ด๊ณ  ์ถ์ ๊ฒ ์๋๋ผ ์ผ๋ถ๋ง ๋ณด๋ด๊ณ  ์ถ๋ค๋ฉด `RedirectAttributes` ์ด์ฉํ์ฌ ๋ค์๊ณผ ๊ฐ์ด ์ฝ๋๋ฅผ ์์ฑํ๋ฉด ๋ฉ๋๋ค. 
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
application.properties ํ์ผ์ ์คํ๋ง๋ถํฐ ๊ธฐ๋ณธ ์ค์ ์ผ๋ก ๋๋์๋ฉด ๋ฉ๋๋ค. 

์ด๋ ๊ฒ ์์ฑํ  ์ RedirectAttributes์ addAttribute๋ฅผ ํตํด ๋ช์ํ ๊ฒ๋ค๋ง ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ๋ฅผ ์ฌ์ฉํด์ ๋ฆฌ๋ค์ด๋ ํธ ํ๋ URL ์ชฝ์ผ๋ก ๋ฐ์ดํฐ๊ฐ ์ ๋ฌ์ด ๋ฉ๋๋ค.

![image](https://user-images.githubusercontent.com/63777714/144978936-59569f23-2d88-4f98-bbdb-99c75df59867.png)

์ด๋ฅผ ๋ฐ๋์ชฝ์์  `@RequestParam` ๋๋ `@ModelAttribute`๋ฅผ ํตํด์ ๋ฐ์์ ์ฌ์ฉํ๋ฉด ๋ฉ๋๋ค. 
> ๐ก ์ฃผ์ <br>
> `@ModelAttrubute`๋ฅผ ์ฌ์ฉํ์ฌ ๊ฐ์ ธ์ฌ ๊ฒฝ์ฐ์ `@SessionAttribute` ์์ ์ฌ์ฉํ ์ด๋ฆ๊ณผ ๋์ผํ ์ด๋ฆ์ ์ฌ์ฉํ๋ฉด ์๋ฉ๋๋ค. <br>
> `@ModelAttrubute`์ ์ฌ์ฉํ  ๊ฒฝ์ฐ Session์์ ์ฐ์ ์ ์ผ๋ก ๊ฐ์ ธ์ค๋ ค๊ณ  ๋ธ๋ ฅํ๋ฉฐ, setComplate ๋ฅผ ํ ๊ฒฝ์ฐ์ session์ด ์กด์ฌํ์ง ์๊ธฐ ๋๋ฌธ์ ์๋ฌ๊ฐ ๋  ์ ์์ต๋๋ค. 

---
## **8. ํธ๋ค๋ฌ ๋ฉ์๋_Flash Attributes**
์ฃผ๋ก ๋ฆฌ๋ค์ด๋ ํธ์์ ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํ  ๋ ์ฌ์ฉํฉ๋๋ค. 
* ๋ฐ์ดํฐ๊ฐ URI์ ๋ธ์ถ๋์ง ์์ต๋๋ค. (๐คทโโ๏ธ ? HttpSession์ ํตํด ๋ฐ์ดํฐ๊ฐ ์ ๋ฌ๋๊ธฐ ๋๋ฌธ์)
* ์์์ ๊ฐ์ฒด๋ฅผ ์ ์ฅํ  ์ ์๋ค.
* ๋ณดํต HTTP ์ธ์์ ์ฌ์ฉํ๋ค.

๋ฆฌ๋ค์ด๋ ํธ ํ๊ธฐ ์ ์ ๋ฐ์ดํฐ๋ฅผ HTTP ์ธ์์ ์ ์ฅํ๊ณ  ๋ฆฌ๋ค์ด๋ ํธ ์์ฒญ์ ์ฒ๋ฆฌ ํ ๋ค์ ๊ทธ ์ฆ์ ์ ๊ฑฐํฉ๋๋ค. 
RedirectAttributes๋ฅผ ํตํด ์ฌ์ฉํ  ์ ์์ต๋๋ค. 

### **์์ ์ฝ๋**
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
    // RedirectAttributes๋ฅผ ํตํด FlashAttribute ๋ฅผ ์ฌ์ฉ 
    redirectAttributes.addFlashAttribute("newEvent", event); // newEvent ๋ผ๋ ์ด๋ฆ์ผ๋ก HttpSession ์ ์ ์ฅ์ด ๋๊ณ , ๋ฆฌ๋ค์ด๋ ํธ ์์ฒญ์ ์ฒ๋ฆฌํ๊ณ  ์ฆ์ ์ ๊ฑฐ๋จ
    return "redirect:/events/list";
}
```

> ๐ต๐ปโโ๏ธ `FlashAttributes`์ `RedirectAttributes`์ ๊ฐ์ฅ ํฐ ์ฐจ์ด ? 
> * `RedirectAttributes`
>   * URI์ ๊ฒฝ๋ก์ ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ๋ก ๋ฐ์ดํฐ๊ฐ ๋ถ๋ ๊ฒ์๋๋ค. 
>   * URI ๊ฒฝ๋ก์ ๋ถ๊ธฐ ๋๋ฌธ์ ๋ฐ๋์ String์ผ๋ก ๋ณํ์ด ๊ฐ๋ฅํ ๊ฐ์ด์ด์ผ ํฉ๋๋ค. (Event์ ๊ฐ์ Entity๋ฅผ ๋ฃ์ด์ค ์ ์์ต๋๋ค. (Converter ๊ฐ ๋ฑ๋ก๋๋ฉด ๊ฐ๋ฅ))
> * `FlashAttributes`
>   * HttpSession ์ ํตํด ๋ฐ์ดํฐ๋ฅผ ๋๊ฒจ์ฃผ๊ธฐ ๋๋ฌธ์ URI์ ๋ฐ์ดํฐ๊ฐ ๋ธ์ถ๋์ง ์์ต๋๋ค. 
>   * Converter๊ฐ ๋ฑ๋ก๋์ด ์์ง ์์๋ Entity ๊ฐ์ฒด๋ฅผ ๋ฐ์ ์ ์์ต๋๋ค. (Session์๋ Object๋ก ๋ฑ๋ก์ด ๊ฐ๋ฅ)

---
## **9. ํธ๋ค๋ฌ ๋ฉ์๋_MultipartFile**
### **MultipartFile**
* ํ์ผ ์๋ก๋์ ์ฌ์ฉํ๋ ๋ฉ์๋ ์๊ท๋จผํธ
* MultipartResolver ๋น์ด ์ค์  ๋์ด ์์ด์ผ ์ฌ์ฉํ  ์ ์๋ค. (์คํ๋ง ๋ถํธ ์๋ ์ค์ ์ด ํด ์ค) 
* POST multipart/form-data ์์ฒญ์ ๋ค์ด์๋ ํ์ผ์ ์ฐธ์กฐํ  ์ ์์ต๋๋ค. 
* List<MultipartFile> ์ํ๋จผํธ๋ก ์ฌ๋ฌ ํ์ผ์ ์ฐธ์กฐํ  ์๋ ์์ต๋๋ค. 

### **ํ์ผ ์๋ก๋ ๊ด๋ จ ์คํ๋ง ๋ถํธ ์ค์ **
* MultipartAutoConfiguration
* MultipartProperties

### **์์ ์ฝ๋**
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
// ํ์คํธ ์ฝ๋
@Test
public void fileUploadTest() throws Exception {
    // ๊ฐ์ง File ๊ฐ์ฒด
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
## **10. ํธ๋ค๋ฌ ๋ฉ์๋_ํ์ผ ๋ค์ด๋ก๋ ๋ฐ ResponseEntity**
### **ํ์ผ ๋ฆฌ์์ค๋ฅผ ์ฝ์ด์ค๋ ๋ฐฉ๋ฒ**
* ์คํ๋ง ResourceLoader ์ฌ์ฉํ๊ธฐ 

### **ํ์ผ ๋ค์ด๋ก๋ ์๋ต ํค๋์ ์ค์ ํ  ๋ด์ฉ**
* Content-Disposition: ์ฌ์ฉ์๊ฐ ํด๋น ํ์ผ์ ๋ฐ์ ๋ ์ฌ์ฉํ  ํ์ผ ์ด๋ฆ
* Content-Type: ์ด๋ค ํ์ผ์ธ๊ฐ
* Content-Length: ์ผ๋ง๋ ํฐ ํ์ผ์ธ๊ฐ

### **ํ์ผ์ ์ข๋ฅ(๋ฏธ๋์ดํ์)์ ์์๋ด๋ ๋ฐฉ๋ฒ**
*  http://tika.apache.org/
* ์๋์ ์์กด์ฑ ์ถ๊ฐ
```xml
<!-- https://mvnrepository.com/artifact/org.apache.tika/tika-core -->
<dependency>
    <groupId>org.apache.tika</groupId>
    <artifactId>tika-core</artifactId>
    <version>2.1.0</version>
</dependency>
```

### **ResponseEntity**
* ์๋ต ์ํ ์ฝ๋
* ์๋ต ํค๋
* ์๋ต ๋ณธ๋ฌธ

### **์์ ์ฝ๋**
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
http://localhost:8080/file/test.png URL ์๋ ฅ์ ๋ค์๊ณผ ๊ฐ์ด ๋ค์ด๋ก๋ ๋ฐ๋ ํ๋ฉด ๋ํ๋ฉ๋๋ค. 

![image](https://user-images.githubusercontent.com/63777714/144994718-b6247661-9c54-49eb-8630-9a81aabc6cad.png)

**์ฐธ๊ณ **
* https://spring.io/guides/gs/uploading-files/
* https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition
* https://www.baeldung.com/java-file-mime-type

---
## **11. ํธ๋ค๋ฌ ๋ฉ์๋_@RequestBody & HttpEntity** 

### **@RequestBody**
* ์์ฒญ ๋ณธ๋ฌธ(body)์ ๋ค์ด์๋ ๋ฐ์ดํฐ๋ฅผ `HttpMessageConveter`๋ฅผ ํตํด ๋ณํํ ๊ฐ์ฒด๋ก
๋ฐ์์ฌ ์ ์์ต๋๋ค.
* `@Valid` ๋๋ `@Validated`๋ฅผ ์ฌ์ฉํด์ ๊ฐ์ ๊ฒ์ฆ ํ  ์ ์์ต๋๋ค.
* `BindingResult` ์๊ท๋จผํธ๋ฅผ ์ฌ์ฉํด ์ฝ๋๋ก ๋ฐ์ธ๋ฉ ๋๋ ๊ฒ์ฆ ์๋ฌ๋ฅผ ํ์ธํ  ์ ์์ต๋๋ค.
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
* ์คํ๋ง MVC ์ค์  (WebMvcConfigurer)์์ ์ค์ ํ  ์ ์๋ค.
* `configureMessageConverters`: ๊ธฐ๋ณธ ๋ฉ์์ง ์ปจ๋ฒํฐ ๋์ฒด
* `extendMessageConverters`: ๋ฉ์์ง ์ปจ๋ฒํฐ์ ์ถ๊ฐ
* ๊ธฐ๋ณธ ์ปจ๋ฒํฐ
    * WebMvcConfigurationSupport.addDefaultHttpMessageConverters

### **HttpEntity**
* ์์ฒญ ๋ณธ๋ฌธ(body)์ ๋ค์ด์๋ ๋ฐ์ดํฐ๋ฅผ `HttpMessageConveter`๋ฅผ ํตํด ๋ณํํ ๊ฐ์ฒด๋ก
๋ฐ์์ฌ ์ ์์ต๋๋ค.
* @RequestBody์ ๋น์ทํ์ง๋ง ์ถ๊ฐ์ ์ผ๋ก ์์ฒญ ํค๋ ์ ๋ณด๋ฅผ ์ฌ์ฉํ  ์ ์์ต๋๋ค.
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
> ํธ๋ค๋ฌ ์ด๋ํฐ๊ฐ `@RequestBody` ๋ก ๋ค์ด์จ Method Arguument ๋ฅผ resolve ํ  ๋ ํธ๋ค๋ฌ ์ด๋ํฐ์ ๋ฑ๋ก๋์ด ์๋ `HttpMessageConverter`๋ฅผ ์ฌ์ฉ์ ํด์ Convert๋ฅผ ์งํํฉ๋๋ค. 

---
## **12. ํธ๋ค๋ฌ ๋ฉ์๋_@ResponseBody & ResponseEntity** 
### **@ResponseBody**
* ๋ฐ์ดํฐ๋ฅผ `HttpMessageConverter`๋ฅผ ์ฌ์ฉํด ์๋ต ๋ณธ๋ฌธ ๋ฉ์์ง๋ก ๋ณด๋ผ ๋ ์ฌ์ฉํฉ๋๋ค.
* `@RestController` ์ฌ์ฉ์ ์๋์ผ๋ก ๋ชจ๋  ํธ๋ค๋ฌ ๋ฉ์๋์ ์ ์ฉ๋ฉ๋๋ค.
### **ResponseEntity**
* ์๋ต ํค๋ ์ํ ์ฝ๋ ๋ณธ๋ฌธ์ ์ง์  ๋ค๋ฃจ๊ณ  ์ถ์ ๊ฒฝ์ฐ์ ์ฌ์ฉํฉ๋๋ค.

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