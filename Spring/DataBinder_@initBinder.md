> π λ³Έ κΈμ μΈνλ° κ°μ 'μ€νλ§ μΉ MVC'λ₯Ό λ£κ³  μ λ¦¬ν κΈμλλ€. 

<br>

# **μ€νλ§ MVC νμ© DataBinder : @InitBinder**
`@InitBinder` μ΄λΈνμ΄μμ νΉμ  μ»¨νΈλ‘€λ¬μμ λ°μΈλ© λλ κ²μ¦ μ€μ μ λ³κ²½νκ³  μΆμ λ μ¬μ©ν©λλ€. 
* λ¦¬ν΄νμμ λ°λμ void μ΄μ΄μΌ ν©λλ€. 
* νλΌλ―Έν°λ‘ `WebDataBinder`λ₯Ό λ°μ μ μμΌλ©°, μ΄λ₯Ό ν΅ν΄ λ°μΈλ© μ€μ μ ν  μ μμ΅λλ€. 
    * λ°μΈλ© μ€μ  `webDataBinder.setDisallowedFields();`
    * ν¬λ§€ν° μ€μ  : `webDataBinder.addCustomFormatter();`
    * Validator(μ»€μ€νν Validator) μ€μ  : `webDataBinder.addValidators();`
    * νΉμ  λͺ¨λΈ κ°μ²΄μλ§ λ°μΈλ© λλ Validator μ€μ μ μ μ©νκ³  μΆμ κ²½μ° : `@InitBinder(βeventβ)`
        * μ΄ κ²½μ°μλ event μ λν λ°μ΄ν°λ₯Ό λ°μΈλ© ν  λλ§ μ μ©μ΄ λ©λλ€. 

**λ°μΈλ© μ€μ  μμ **
```java
@InitBinder
public void initEventBinder (WebDataBinder webDataBinder) {
    webDataBinder.setDisallowedFields("id"); // DisallowedFields : λ°κ³ μΆμ§ μμ νλκ°μ μ€μ ν  μ μμ΅λλ€. 
}
```

**ν¬λ§€ν° μ€μ  μμ **
```java
// λ€μκ³Ό κ°μ΄ μνλ ν¬λ§€ν°λ₯Ό μ€μ ν΄μ€ μ μμ΅λλ€. 
@DateTimeFormat(iso = ISO.DATE)
private LocalDate startDate;
```
**Validator(μ»€μ€νν Validator) μ€μ  μμ **
```java
public class EventValidator implements Validator {

    @Override
    // μ΄λ ν λλ©μΈ ν΄λμ€μ λν Validationμ μ§μνλ κ²μΈμ§ νλ¨νλ λ©μλ
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
    // μ€μ ν Validator initBinderμ λ±λ‘ν΄μ£ΌκΈ°
    webDataBinder.setValidator(new EventValidator());
}
```
name νλμ aaaλ₯Ό μλ ₯νλ λ€μκ³Ό κ°μ΄ λ΄μ΅λλ€. μ μμ μΌλ‘ Validator κ° μ μ μ©λ κ²μ λ³Ό μ μμ΅λλ€.

![image](https://user-images.githubusercontent.com/63777714/145322983-1e01d7c3-e39c-44d5-8952-fe44747b9f1f.png)

λλ `EventValidator` ν΄λμ€μ `@Component` λ₯Ό μλ ₯ν΄ λΉμΌλ‘ λ±λ‘ν ν λΉμ μ£Όμλ°μμ μνλ μμ μ validationμ μ§νν  μλ μμ΅λλ€. 


**μ°Έκ³ **
* https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-initbinder
* https://github.com/spring-projects/spring-petclinic/blob/master/src/main/java/org/springframework/samples/petclinic/owner/PetController.java 
