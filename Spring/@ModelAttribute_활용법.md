> 📚 본 글은 인프런 강의 '스프링 웹 MVC'를 듣고 정리한 글입니다. 

<br>

# **스프링 MVC 활용 @ModelAttribute의 또 다른 활용법**

## **@ModelAttribute 의 또 다른 활용법**
만약 하나의 컨트롤러 안에서 모든 메소드가 참고해야하는 어떠한 `Model` 정보가 있을 때 매번 `Model.attrubute`를 사용해서 넣어주기 번거로울 때 다음과 같이 사용할 수 있습니다. 

1. `@ModelAttribute` 어노테이션을 붙인 메소드를 하나 따로 만든다. 
2. 만든 메소드 안에 공통적으로 사용할 Model을 attribute 사용하여 넣는다. 

```java
// Model.addAttribute 를 사용하여 넣는 방법
@ModelAttribute
    public void categories(Model model) {
        model.addAttribute("categories", List.of("study", "hobby", "seminar"));
    }

// 미리 categories라는 이름을 지정해서 어노테이션을 선언하는 방법
@ModelAttribute("categories")
public List<String> categories2(Model model) {
    return List.of("study", "hobby", "seminar");
}
```

또한, @RequestMapping 과 같이 사용도 가능합니다. @RequestMapping 과 같이 사용하면 해당 메소드에서 리턴하는 객체를 Model 에 넣어줍니다. 예제코드는 다음과 같습니다.

```java
@GetMapping("/events/form/name")
@ModelAttribute // (생략도 가능)
public Event eventsFormName(Model model) {
    return new Event();
}
```
코드에 적어놨지만 `@ModelAttribute`는 생략도 가능합니다. 그러니 생략까지 하면 코드가 훨씬 간결해지겠죠? 
view 이름은 `RequestToViewNameTranslator` 가 요청과 동일한 view를 찾아서 리턴해줍니다. 뷰 이름과 동일하지 않다면 리턴을 제대로 못해줄 것 같습니다. 


정리하자면 다음과 같습니다. 
**@ModelAttribute의 다른 용법**
1. `@RequestMapping`을 사용한 핸들러 메소드의 아규먼트에 사용하기
2.` @Controller` 또는 `@ControllerAdvice` 를 사용한 클래스에서 모델 정보를 초기화 할 때 사용
3. `@RequestMapping`과 같이 사용하면 해당 메소드에서 리턴하는 객체를 모델에 넣어줌
    * `RequestToViewNameTranslator`를 사용하여 view 를 찾아줌