> 📚 본 글은 인프런 강의 '스프링 웹 MVC'를 듣고 정리한 글입니다. 

<br>

# **스프링 MVC 활용 DataBinder : @InitBinder**
`@InitBinder` 어노테이션은 특정 컨트롤러에서 바인딩 또는 검증 설정을 변경하고 싶을 때 사용합니다. 
* 리턴타입은 반드시 void 이어야 합니다. 
* 파라미터로 `WebDataBinder`를 받을 수 있으며, 이를 통해 바인딩 설정을 할 수 있습니다. 
    * 바인딩 설정 `webDataBinder.setDisallowedFields();`
    * 포매터 설정 : `webDataBinder.addCustomFormatter();`
    * Validator(커스텀한 Validator) 설정 : `webDataBinder.addValidators();`
    * 특정 모델 객체에만 바인딩 또는 Validator 설정을 적용하고 싶은 경우 : `@InitBinder(“event”)`
        * 이 경우에는 event 에 대한 데이터를 바인딩 할 때만 적용이 됩니다. 

**바인딩 설정 예제**
```java
@InitBinder
public void initEventBinder (WebDataBinder webDataBinder) {
    webDataBinder.setDisallowedFields("id"); // DisallowedFields : 받고싶지 않은 필드값을 설정할 수 있습니다. 
}
```

**포매터 설정 예제**
```java
// 다음과 같이 원하는 포매터를 설정해줄 수 있습니다. 
@DateTimeFormat(iso = ISO.DATE)
private LocalDate startDate;
```
**Validator(커스텀한 Validator) 설정 예제**
```java
public class EventValidator implements Validator {

    @Override
    // 어떠한 도메인 클래스에 대한 Validation을 지원하는 것인지 판단하는 메소드
    public boolean supports(Class<?> clazz) {
        return Event.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        Event event = (Event) target;
        if (event.getName().equalsIgnoreCase("aaa")) {
            errors.rejectValue("name","wrongValue","the valus is not allowed");
        }
    }
}
```
```java
@InitBinder
public void initEventBinder (WebDataBinder webDataBinder) {
    // 설정한 Validator initBinder에 등록해주기
    webDataBinder.setValidator(new EventValidator());
}
```
name 필드에 aaa를 입력하니 다음과 같이 떴습니다. 정상적으로 Validator 가 잘 적용된 것을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/63777714/145322983-1e01d7c3-e39c-44d5-8952-fe44747b9f1f.png)

또는 `EventValidator` 클래스에 `@Component` 를 입력해 빈으로 등록한 후 빈을 주입받아서 원하는 시점에 validation을 진행할 수도 있습니다. 


**참고**
* https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-initbinder
* https://github.com/spring-projects/spring-petclinic/blob/master/src/main/java/org/springframework/samples/petclinic/owner/PetController.java 
