> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>


# **Validation 추상화** 
 
## **Validator란**
스프링의 Validator는 스프링에서 범용적으로 사용할 수 있는 객체 검증기를 정의할 수 있는 API입니다. @Controller로 HTTP 요청을 @ModelAttribute 모델에 바인딩할 때 주로 사용되며, 비즈니스 로직에서 검증 로직을 분리하고 싶을 때도 사용할 수 있습니다. 

### 특징
* 어떤한 계층과도 관계가 없다. => 모든 계층(웹, 서비스, 데이터)에서 사용해도 좋습니다. 
* 구현체 중 하나로, JSR-303(Bean Validation 1.0)과 JSR-349(Bean Validation 1.1)을
지원합니다. (<a href = 'https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/beanvalidation/LocalValidatorFactoryBean.html'>LocalValidatorFactoryBean</a>)
* DataBinder에 들어가 바인딩 할 때 같이 사용되기도 합니다. 

 `org.springframework.validation`의 인터페이스를 살펴보면 다음과 같습니다.

 ```java
package org.springframework.validation;

public interface Validator {
    // 검증할 수 있는 객체 타입인지 확인해주는 메소드
    boolean supports(Class<?> clazz);
    // supports() 메소드를 통과해야지만 validate() 메소드를 호출할 수 있으며,
    // 바인딩이 완료된 객체를 이용해 값을 검증하는 코드를 작성합니다. 
    void validate(Object target, Errors errors);
}
```
Validator를 사용하려면 `supports()`, `validate()` 총 두개의 메소드를 구현해야합니다. 
실습코드를 통해 직접 구현해보도록 하겠습니다. 


Event.java
```java
public class Event {
    Integer id;
    String title;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }
}
```
EventValidate.java
```java
import org.springframework.validation.Errors;
import org.springframework.validation.ValidationUtils;
import org.springframework.validation.Validator;

public class EventValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return Event.class.isAssignableFrom(clazz); // 
    }

    @Override
    public void validate(Object target, Errors errors) {
        // 구현할 땐 ValidationUtils 를 사용할 수 있어 편리하다. 
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "title","notEmpty","Empty title is not allowed.");
    }
}
```
AppRunner.java (실행코드)
```java
 @Component
public class AppRunner implements ApplicationRunner {
    
    @Override
    public void run(ApplicationArguments args) throws Exception {
        Event event = new Event(); // 1. 이벤트 생성
        EventValidator eventValidator = new EventValidator(); // 2. 이벤트 Validator 생성
        // BeanPropertyBindingResult : 명시한 클래스에서 발생하는 오류를 저장하는 역할만 하는 객체
        Errors errors = new BeanPropertyBindingResult(event, "event"); // 3. 에러 생성

        eventValidator.validate(event, errors); // 4.validate 를 진행한다.

        System.out.println(errors.hasErrors()); // 에러가 발생했는지 확인

        // 모든 에러를 가져온 뒤(getAllErrors), 순차적으로 순회를 하며 에러를 찍는다. 
        errors.getAllErrors().forEach(e -> {
            System.out.println("========= error code =========");
            Arrays.stream(e.getCodes()).forEach(System.out::println);
            System.out.println(e.getDefaultMessage());
        });
    }
}
```

> 실행결과<br>
>true
========= error code =========<br>
notEmpty.event.title<br>
notEmpty.title<br>
notEmpty.java.lang.String<br>
notEmpty<br> 
Empty title is not allowed.

----

## Spring Boot 2.0.5 이상 버전을 사용할 때
* **LocalValidatorFactoryBean** 빈으로 자동 등록합니다. 
* JSR-380(Bean Validation 2.0.1) 구현체로 hibernate-validator 사용합니다. 

Event.java
```java
public class Event {
    Integer id;

    @NotEmpty // 비어있으면 안된다는 어노테이션
    String title;

    @Min(0) // 최소사이즈 어노테이션
    Integer limit;

    @Email // 이메일을 나타내는 어노테이션
    String email;

   // getter,setter  생략
}
```

AppRunner.java
```java
@Component
public class AppRunner implements ApplicationRunner {
    @Autowired
    Validator validator;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(validator.getClass());

        Event event = new Event();  
        event.setLimit(-1); // 에러생성
        event.setEmail("aaaa3344");

        Errors errors = new BeanPropertyBindingResult(event, "event");

        validator.validate(event,errors);

        System.out.println(errors.hasErrors());
        // 모든 에러를 가져온 뒤(getAllErrors), 순차적으로 순회를 하며 에러를 찍는다.
        errors.getAllErrors().forEach(e -> {
            System.out.println("========= error code =========");
            Arrays.stream(e.getCodes()).forEach(System.out::println);
            System.out.println(e.getDefaultMessage());
        });
    }
}
```

> 실행결과<br>
true<br>
========= error code =========<br>
NotEmpty.event.title<br>
NotEmpty.title<br>
NotEmpty.java.lang.String<br>
NotEmpty<br>
비어 있을 수 없습니다<br>
========= error code =========<br>
Min.event.limit<br>
Min.limit<br>
Min.java.lang.Integer<br>
Min<br>
0 이상이어야 합니다<br>
========= error code =========<br>
Email.event.email<br>
Email.email<br>
Email.java.lang.String<br>
Email<br>
올바른 형식의 이메일 주소여야 합니다<br>


이처럼 Spring Boot 2.0.5 이상부터는 간단한 검증은 EventValidator 없이도 검증이 가능합니다. 복잡한 로직에 대해서는 Validator를 만들어서 검증을 해야합니다. 