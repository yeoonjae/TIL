> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ์น MVC'๋ฅผ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>

# **@ExceptionHandler, @(Rest)CnotrollerAdvice**

## **@ExceptionHandler**
 `@ExceptionHandler`๋ ํน์  ์์ธ๊ฐ ๋ฐ์ํ ์์ฒญ์ ์ฒ๋ฆฌํ๋ ํธ๋ค๋ฌ์๋๋ค. MVC ์ปจํธ๋กค๋ฌ ์์์ ์ด๋ค ์์ฒญ์ ์ฒ๋ฆฌํ๋ค๊ฐ ์๋ฌ๋ฅผ ์ง์  ๋ง๋ค์ด ๋ฐ์์ํค๊ฑฐ๋ ๋๋ ์๋ฐ์์ ๊ธฐ๋ณธ์ ์ผ๋ก ์ง์ํ๋ ์์ธ๊ฐ ๋ฐ์ํ์ ๋, `@ExceptionHandler`๋ก ์ ์ํด์ ๊ทธ ์์ธ๋ค์ ์ด๋ป๊ฒ ์๋ต์ ๋ณด๋ผ์ง๋ฅผ ์ค์ ํ  ์ ์์ต๋๋ค. 

* ์ง์ํ๋ ๋ฉ์๋ ์๊ท๋จผํธ๋ฅผ ์ ์ํ  ์ ์์ต๋๋ค. (์๋ url ์ฐธ๊ณ ) (ํด๋น ์์ธ ๊ฐ์ฒด, ํธ๋ค๋ฌ ๊ฐ์ฒด, ...)
* ์ง์ํ๋ ๋ฆฌํด ๊ฐ์ ๊ธฐ๋ณธ ๋ฉ์๋์ ๋ฆฌํด๊ฐ์ ๋๋ถ๋ถ ์ง์ํฉ๋๋ค. 
* REST API์ ๊ฒฝ์ฐ ์๋ต ๋ณธ๋ฌธ์ ์๋ฌ์ ๋ํ ์ ๋ณด๋ฅผ ๋ด์์ฃผ๊ณ , ์ํ ์ฝ๋๋ฅผ ์ค์ ํ๋ ค๋ฉด ResponseEntity๋ฅผ ์ฃผ๋ก ์ฌ์ฉํฉ๋๋ค.

๋ง์ฝ, ์ฌ๋ฌ๊ฐ์ ์๋ฌ๋ฅผ ์ฒ๋ฆฌํ๋ ํธ๋ค๋ฌ๋ฅผ ์ ์ํ๊ณ  ์ถ๋ค๋ฉด ๋ค์๊ณผ ๊ฐ์ด ์ ์ํ  ์ ์์ต๋๋ค. ์ด์ ๊ฐ์ ๊ฒฝ์ฐ์ ์๊ท๋จผํธ๋ฅผ ๋ชจ๋  ์๋ฌ๋ฅผ ๋ค ๋ฐ์ ์ ์๋ ์์ํ์์ ์๋ฌ๋ก ์ ์ํด์ผ ํฉ๋๋ค. 
```java
@ExceptionHandler({EventException.class, RuntimeException.class})
public String eventErrorHandler(RuntimeException exception, Model model) {
    model.addAttribute("message", "event,runtime error");
    return "error";
}
```

REST API ์ ๊ฒฝ์ฐ ์์ 
```java
@ExceptionHandler
public ResponseEntity errorHandler() {
    return ResponseEntity.badRequest().body("can't create event as ... ");
}
```

## **@(Rest)CnotrollerAdvice**
์์์ ์ค๋ชํ๋ `@ExceptionHandler` ๋ ์ ์ธํ ์ปจํธ๋กค๋ฌ ์์์์ ์์ธ๋ฅผ ์ฒ๋ฆฌํ๋ ๊ฒ์ด๊ณ , ์ฌ๋ฌ ์ปจํธ๋กค๋ฌ ํน์ ๋ชจ๋  ์ปจํธ๋กค๋ฌ์ ์ ์ญ์ ์ผ๋ก ์ ์ฉํ๊ณ  ์ถ์ ๊ฒฝ์ฐ์ ์ฌ์ฉํ๋ ์ด๋ธํ์ด์ ์๋๋ค. 

์์ธ ์ฒ๋ฆฌ, ๋ฐ์ธ๋ฉ ์ค์ , ๋ชจ๋ธ ๊ฐ์ฒด๋ฅผ ๋ชจ๋  ์ปจํธ๋กค๋ฌ ์ ๋ฐ์ ๊ฑธ์ณ ์ ์ฉํ๊ณ  ์ถ์ ๊ฒฝ์ฐ์ ์ฌ์ฉํฉ๋๋ค. 
* `@ExceptionHandler`
* `@InitBinder`
* `@ModelAttributes`

```java
@ControllerAdvice
public class BaseController {
    @ExceptionHandler({EventException.class, RuntimeException.class})
    public String eventErrorHandler(RuntimeException exception, Model model) {
        model.addAttribute("message", "event,runtime error");
        return "error";
    }

    @InitBinder
    public void initEventBinder (WebDataBinder webDataBinder) {
        webDataBinder.setDisallowedFields("id");
        webDataBinder.setValidator(new EventValidator());
    }

    @ModelAttribute
    public void categories(Model model) {
        model.addAttribute("categories", List.of("study", "hobby", "seminar"));
    }

    @ModelAttribute("categories")
    public List<String> categories2(Model model) {
        return List.of("study", "hobby", "seminar");
    }
}
```



๋ํ, ์ ์ฉํ  ๋ฒ์๋ฅผ ์ง์ ํ  ์๋ ์์ต๋๋ค. 
* ํน์  ์ ๋ธํ์ด์์ ๊ฐ์ง๊ณ  ์๋ ์ปจํธ๋กค๋ฌ์๋ง ์ ์ฉํ๊ธฐ
* ํน์  ํจํค์ง ์ดํ์ ์ปจํธ๋กค๋ฌ์๋ง ์ ์ฉํ๊ธฐ
* ํน์  ํด๋์ค ํ์์๋ง ์ ์ฉํ๊ธฐ


 **์ฐธ๊ณ **
* https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-exceptionhandler-args
*  https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-controller-advice