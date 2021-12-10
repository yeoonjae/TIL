> 📚 본 글은 인프런 강의 '스프링 웹 MVC'를 듣고 정리한 글입니다. 

<br>

# **@ExceptionHandler, @(Rest)CnotrollerAdvice**

## **@ExceptionHandler**
 `@ExceptionHandler`는 특정 예외가 발생한 요청을 처리하는 핸들러입니다. MVC 컨트롤러 안에서 어떤 요청을 처리하다가 에러를 직접 만들어 발생시키거나 또는 자바에서 기본적으로 지원하는 예외가 발생했을 때, `@ExceptionHandler`로 정의해서 그 예외들을 어떻게 응답을 보낼지를 설정할 수 있습니다. 

* 지원하는 메소드 아규먼트를 정의할 수 있습니다. (아래 url 참고) (해당 예외 객체, 핸들러 객체, ...)
* 지원하는 리턴 값은 기본 메소드의 리턴값을 대부분 지원합니다. 
* REST API의 경우 응답 본문에 에러에 대한 정보를 담아주고, 상태 코드를 설정하려면 ResponseEntity를 주로 사용합니다.

만약, 여러개의 에러를 처리하는 핸들러를 정의하고 싶다면 다음과 같이 정의할 수 있습니다. 이와 같은 경우엔 아규먼트를 모든 에러를 다 받을 수 있는 상위타입의 에러로 정의해야 합니다. 
```java
@ExceptionHandler({EventException.class, RuntimeException.class})
public String eventErrorHandler(RuntimeException exception, Model model) {
    model.addAttribute("message", "event,runtime error");
    return "error";
}
```

REST API 의 경우 예제
```java
@ExceptionHandler
public ResponseEntity errorHandler() {
    return ResponseEntity.badRequest().body("can't create event as ... ");
}
```

## **@(Rest)CnotrollerAdvice**
위에서 설명했던 `@ExceptionHandler` 는 선언한 컨트롤러 안에서의 예외를 처리하는 것이고, 여러 컨트롤러 혹은 모든 컨트롤러에 전역적으로 적용하고 싶은 경우에 사용하는 어노테이션 입니다. 

예외 처리, 바인딩 설정, 모델 객체를 모든 컨트롤러 전반에 걸쳐 적용하고 싶은 경우에 사용합니다. 
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



또한, 적용할 범위를 지정할 수도 있습니다. 
* 특정 애노테이션을 가지고 있는 컨트롤러에만 적용하기
* 특정 패키지 이하의 컨트롤러에만 적용하기
* 특정 클래스 타입에만 적용하기


 **참고**
* https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-exceptionhandler-args
*  https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-controller-advice