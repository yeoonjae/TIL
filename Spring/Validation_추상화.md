> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ํต์ฌ ๊ธฐ์ '์ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>


# **Validation ์ถ์ํ** 
 
## **Validator๋**
์คํ๋ง์ Validator๋ ์คํ๋ง์์ ๋ฒ์ฉ์ ์ผ๋ก ์ฌ์ฉํ  ์ ์๋ ๊ฐ์ฒด ๊ฒ์ฆ๊ธฐ๋ฅผ ์ ์ํ  ์ ์๋ API์๋๋ค. @Controller๋ก HTTP ์์ฒญ์ @ModelAttribute ๋ชจ๋ธ์ ๋ฐ์ธ๋ฉํ  ๋ ์ฃผ๋ก ์ฌ์ฉ๋๋ฉฐ, ๋น์ฆ๋์ค ๋ก์ง์์ ๊ฒ์ฆ ๋ก์ง์ ๋ถ๋ฆฌํ๊ณ  ์ถ์ ๋๋ ์ฌ์ฉํ  ์ ์์ต๋๋ค. 

### ํน์ง
* ์ด๋คํ ๊ณ์ธต๊ณผ๋ ๊ด๊ณ๊ฐ ์๋ค. => ๋ชจ๋  ๊ณ์ธต(์น, ์๋น์ค, ๋ฐ์ดํฐ)์์ ์ฌ์ฉํด๋ ์ข์ต๋๋ค. 
* ๊ตฌํ์ฒด ์ค ํ๋๋ก, JSR-303(Bean Validation 1.0)๊ณผ JSR-349(Bean Validation 1.1)์
์ง์ํฉ๋๋ค. (<a href = 'https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/beanvalidation/LocalValidatorFactoryBean.html'>LocalValidatorFactoryBean</a>)
* DataBinder์ ๋ค์ด๊ฐ ๋ฐ์ธ๋ฉ ํ  ๋ ๊ฐ์ด ์ฌ์ฉ๋๊ธฐ๋ ํฉ๋๋ค. 

 `org.springframework.validation`์ ์ธํฐํ์ด์ค๋ฅผ ์ดํด๋ณด๋ฉด ๋ค์๊ณผ ๊ฐ์ต๋๋ค.

 ```java
package org.springframework.validation;

public interface Validator {
    // ๊ฒ์ฆํ  ์ ์๋ ๊ฐ์ฒด ํ์์ธ์ง ํ์ธํด์ฃผ๋ ๋ฉ์๋
    boolean supports(Class<?> clazz);
    // supports() ๋ฉ์๋๋ฅผ ํต๊ณผํด์ผ์ง๋ง validate() ๋ฉ์๋๋ฅผ ํธ์ถํ  ์ ์์ผ๋ฉฐ,
    // ๋ฐ์ธ๋ฉ์ด ์๋ฃ๋ ๊ฐ์ฒด๋ฅผ ์ด์ฉํด ๊ฐ์ ๊ฒ์ฆํ๋ ์ฝ๋๋ฅผ ์์ฑํฉ๋๋ค. 
    void validate(Object target, Errors errors);
}
```
Validator๋ฅผ ์ฌ์ฉํ๋ ค๋ฉด `supports()`, `validate()` ์ด ๋๊ฐ์ ๋ฉ์๋๋ฅผ ๊ตฌํํด์ผํฉ๋๋ค. 
์ค์ต์ฝ๋๋ฅผ ํตํด ์ง์  ๊ตฌํํด๋ณด๋๋ก ํ๊ฒ ์ต๋๋ค. 


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
        // ๊ตฌํํ  ๋ ValidationUtils ๋ฅผ ์ฌ์ฉํ  ์ ์์ด ํธ๋ฆฌํ๋ค. 
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "title","notEmpty","Empty title is not allowed.");
    }
}
```
AppRunner.java (์คํ์ฝ๋)
```java
 @Component
public class AppRunner implements ApplicationRunner {
    
    @Override
    public void run(ApplicationArguments args) throws Exception {
        Event event = new Event(); // 1. ์ด๋ฒคํธ ์์ฑ
        EventValidator eventValidator = new EventValidator(); // 2. ์ด๋ฒคํธ Validator ์์ฑ
        // BeanPropertyBindingResult : ๋ช์ํ ํด๋์ค์์ ๋ฐ์ํ๋ ์ค๋ฅ๋ฅผ ์ ์ฅํ๋ ์ญํ ๋ง ํ๋ ๊ฐ์ฒด
        Errors errors = new BeanPropertyBindingResult(event, "event"); // 3. ์๋ฌ ์์ฑ

        eventValidator.validate(event, errors); // 4.validate ๋ฅผ ์งํํ๋ค.

        System.out.println(errors.hasErrors()); // ์๋ฌ๊ฐ ๋ฐ์ํ๋์ง ํ์ธ

        // ๋ชจ๋  ์๋ฌ๋ฅผ ๊ฐ์ ธ์จ ๋ค(getAllErrors), ์์ฐจ์ ์ผ๋ก ์ํ๋ฅผ ํ๋ฉฐ ์๋ฌ๋ฅผ ์ฐ๋๋ค. 
        errors.getAllErrors().forEach(e -> {
            System.out.println("========= error code =========");
            Arrays.stream(e.getCodes()).forEach(System.out::println);
            System.out.println(e.getDefaultMessage());
        });
    }
}
```

> ์คํ๊ฒฐ๊ณผ<br>
>true
========= error code =========<br>
notEmpty.event.title<br>
notEmpty.title<br>
notEmpty.java.lang.String<br>
notEmpty<br> 
Empty title is not allowed.

----

## Spring Boot 2.0.5 ์ด์ ๋ฒ์ ์ ์ฌ์ฉํ  ๋
* **LocalValidatorFactoryBean** ๋น์ผ๋ก ์๋ ๋ฑ๋กํฉ๋๋ค. 
* JSR-380(Bean Validation 2.0.1) ๊ตฌํ์ฒด๋ก hibernate-validator ์ฌ์ฉํฉ๋๋ค. 

Event.java
```java
public class Event {
    Integer id;

    @NotEmpty // ๋น์ด์์ผ๋ฉด ์๋๋ค๋ ์ด๋ธํ์ด์
    String title;

    @Min(0) // ์ต์์ฌ์ด์ฆ ์ด๋ธํ์ด์
    Integer limit;

    @Email // ์ด๋ฉ์ผ์ ๋ํ๋ด๋ ์ด๋ธํ์ด์
    String email;

   // getter,setter  ์๋ต
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
        event.setLimit(-1); // ์๋ฌ์์ฑ
        event.setEmail("aaaa3344");

        Errors errors = new BeanPropertyBindingResult(event, "event");

        validator.validate(event,errors);

        System.out.println(errors.hasErrors());
        // ๋ชจ๋  ์๋ฌ๋ฅผ ๊ฐ์ ธ์จ ๋ค(getAllErrors), ์์ฐจ์ ์ผ๋ก ์ํ๋ฅผ ํ๋ฉฐ ์๋ฌ๋ฅผ ์ฐ๋๋ค.
        errors.getAllErrors().forEach(e -> {
            System.out.println("========= error code =========");
            Arrays.stream(e.getCodes()).forEach(System.out::println);
            System.out.println(e.getDefaultMessage());
        });
    }
}
```

> ์คํ๊ฒฐ๊ณผ<br>
true<br>
========= error code =========<br>
NotEmpty.event.title<br>
NotEmpty.title<br>
NotEmpty.java.lang.String<br>
NotEmpty<br>
๋น์ด ์์ ์ ์์ต๋๋ค<br>
========= error code =========<br>
Min.event.limit<br>
Min.limit<br>
Min.java.lang.Integer<br>
Min<br>
0 ์ด์์ด์ด์ผ ํฉ๋๋ค<br>
========= error code =========<br>
Email.event.email<br>
Email.email<br>
Email.java.lang.String<br>
Email<br>
์ฌ๋ฐ๋ฅธ ํ์์ ์ด๋ฉ์ผ ์ฃผ์์ฌ์ผ ํฉ๋๋ค<br>


์ด์ฒ๋ผ Spring Boot 2.0.5 ์ด์๋ถํฐ๋ ๊ฐ๋จํ ๊ฒ์ฆ์ EventValidator ์์ด๋ ๊ฒ์ฆ์ด ๊ฐ๋ฅํฉ๋๋ค. ๋ณต์กํ ๋ก์ง์ ๋ํด์๋ Validator๋ฅผ ๋ง๋ค์ด์ ๊ฒ์ฆ์ ํด์ผํฉ๋๋ค. 