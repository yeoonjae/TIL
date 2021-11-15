> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>


# **DataBinder 추상화** 
  데이터 바인딩이란 기술적인 관점에서 봤을 때 Property 의 값을 target 객체에 설정하는 기능이며, 사용자의 관점에서 봤을 땐 사용자 입력값을 애플리케이션 도메인 모델에 동적으로 변환하여 넣어주는 기능입니다. 

  예를 들어, 사용자는 "12345"이라는 문자열을 입력합니다. 이 "12345"이라는 값을 int, long 또는 User 등 다양한 도메인 타입으로 변환해서 넣어주는 기능을 말합니다. 

스프링 초창기에는 PropertyEditor 인터페이스를 사용하여 데이터 바인딩을 하였지만, 후에 이를 대체할 Converter와 Formatter란 데이터 바인딩 인터페이스가 나오게 되었습니다. 

Converter는 S타입을 T 타입으로 변환할 수 있는 일반적인 변환기이며 쓰레드세이프합니다. Formatter는 Object와 String간의 변환을 담당하며 문자열을 Locale에 따라 다국화하는 기능을 제공합니다. 웹 어플리케이션을 제작할 때는 주로 Formatter를 사용합니다.


---
  ## **Converter**
  * Converter는 제네릭으로 두 개의 인자, Source와 Target을 받아서 구현합니다. 
  * S타입을 T 타입으로 변환할 수 있는 일반적인 변환기입니다. 
  * Converter는 PropertyEditor와는 다르게 Thread-safe하기 때문에 스프링 빈으로 등록해서 사용 가능합니다.

### **Converter 실습코드**

 ```java
 package me.whiteship.dempspring51;

import org.springframework.core.convert.converter.Converter;
import org.springframework.stereotype.Component;

public class EventConverter {

    // converter는 소스와 타겟 두개의 인자를 받는다 이 클래스의 경우 String타입을 소스로 받아 Event 타입으로 변환시키는 것이다.
    public static class StringToEventConverter implements Converter<String, Event> {

        @Override
        public Event convert(String source) {
            // 소스(String)을 받아서 Event로 변환하면 된다. 
            return new Event(Integer.parseInt(source));
        }

    }

    // converter는 소스와 타겟 두개의 인자를 받는다 이 클래스의 경우 Event 타입을 소스로 받아 String 타입으로 변환시키는 것이다.
    public static class StringToStringConverter implements Converter<Event, String> {

        @Override
        public String convert(Event source) {
            return source.getId().toString();
        }

    }
}

 ```

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        // registry에 addConverter 로 EventConerter클래스에서 만든 StringToEventConverter를 등록해줍니다. 
        registry.addConverter(new StringToEventConverter());
    }
}
```
 - Converter를 등록하려면 Spring MVC 에서는 `WebMvcConfigurer` 인터페이스를 구현한 
 클래스를 작성해야합니다. `addFormatters` 메소드를 오버라이딩하여 등록합니다. 
 

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class EventControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void getTest() throws Exception {
        mockMvc.perform(get("/event/1"))
            .andExpect(status().isOk())
            .andExpect(content().string("1"));
    }
}
```
* WebConfig 클래스에 넣어준 Converter가 모든 Controller에 동작합니다. 
* 따라서 EventControllerTest에서 요청한 1 이 Converter에서 Event로 변환이 되어, Event Type으로 받을 수 있습니다. 

EventControllerTest를 실행한 결과는 다음과 같습니다. 
   
```java 
Event{id=1, title='null'}
```

### **정리**
  * S 타입을 T 타입으로 변환할 수 있는 일반적인 변환기
  * 상태 정보 없음 == `Stateless` == 쓰레드 세이프
  * `ConverterRegistry`에 등록하여 사용
  * `Converter<String, Event>` 로 사용되면 `String` 타입을 `source`로 받아 `Event` `Type`으로 변환한다는 것입니다. 


---
## **Fomatter**
 * Formatter는 제네릭으로 한 개의 인자만을 받습니다. 
 * **Object와 String간의 변환을 담당하기 때문에 다른 하나의 인자는 String으로 정해져 있기 때문**입니다. 


 Formatter는 `parse(String text, Locale locale)` , `print(변환할 객체 object, Locale locale)` 이 두가지 메소드를 오버라이딩 해야합니다. 

 또한 상태를 갖고있지 않아서 **<u>스레드세이프</u>** 합니다. 따라서 `@Component` 어노테이션을 붙여 Bean으로 등록이 가능합니다. 

 ### **Fomatter 실습코드**
```java
public class EventFormatter implements Formatter<Event> {

    @Override
    public Event parse(String text, Locale locale) throws ParseException {
        return new Event(Integer.parseInt(text));
    }

    @Override
    public String print(Event object, Locale locale) {
        return object.getId().toString();
    }
    
}
```

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
//        registry.addConverter(new StringToEventConverter()); // Converter 등록방법
        registry.addFormatter(new EventFormatter());
    }
}

```

테스트 결과는 다음과 같습니다. 

```java
Event{id=1, title='null'}
```

### **정리**
 * PropertyEditor 대체제로 사용
 * Object와 String 간의 변환을 담당
 * 문자열을 Locale에 따라 다국화 하는 기능도 제공. (optional)
 * FormatterReguistry에 등록하여 사용

---

 ## **ConversionService**
<br>

![image](https://user-images.githubusercontent.com/63777714/141680785-8c29f74d-80bb-42ed-b497-e0eb92a47664.png)

`ConversionService`는 실제 데이터 변환이 일어나는 곳입니다. `Converter`와 `formatter` 또한 SpringMVC의 경우 `WebMvcConfigurer`를 통해 `ConversionService`에 등록이 되며  `ConversionService`에서 실제 데이터 변환이 일어납니다. 

SpringBoot에서 `ConversionService`를 사용하는 코드를 작성해보겠습니다. 

```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ConversionService conversionService;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(conversionService.getClass().toString());
    }
}
```


위 파일의 실행 결과는 다음과 같습니다. 

```java
class org.springframework.boot.autoconfigure.web.format.WebConversionService
```

`ConversionService`는 `DefaultFormattingConversionService`가 나오는 것이 아니라 `WebConversionService`가 나온 것을 확인할 수 있습니다. 

🤷‍♂️ 어째서 이런 결과가 나오는 것일까요? 

> 바로 Spring-Boot 환경에서 실행했기 때문입니다. `WebConversionService`는 Spring-Boot에서 제공해주는 클래스로 `DefaultFormattingConversionService`를 상속하여 만든 것으로 빈으로 등록해줍니다. <br><br>
> 또한, Spring-Boot에서는 WebConfig 클래스 파일이 없어도 (Registry를 사용하지 않아도) 어노테이션을 통해 Bean으로 등록해준다면 Formatter와 Converter Bean을 찾아서 자동으로 등록해줍니다. 

---
 ## **WebConversionService**
* Spring-Boot 가 제공해주는 클래스로 `DefaultFormattingConversionService`를 상속받아 구현된 클래스입니다. 빈으로 등록된 `Formatter`와 `Converter`를 자동으로 등록해주어 `WebConversionService` 인터페이스를 구현할 필요가 없습니다. 

```java
@Component
public class EventFormatter implements Formatter<Event> {

    @Override
    public Event parse(String text, Locale locale) throws ParseException {
        return new Event(Integer.parseInt(text));
    }

    @Override
    public String print(Event object, Locale locale) {
        return object.getId().toString();
    }
}

```
```java
public class EventConverter  {

    @Component
    public static class StringToEventConverter implements Converter<String, Event> {
        @Override
        public Event convert(String source){
            return new Event(Integer.parseInt(source));
        }
    }

    @Component
    public static class EventToStringConverter implements Converter<Event, String>{
        @Override
        public String convert(Event source){
            return source.getId().toString();
        }
    }
}

```
```java
@RestController
public class EventController {

    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event){
        System.out.println(event);
        return event.getId().toString();
    }
}
```

Test 코드인 경우
```java
@RunWith(SpringRunner.class)
@WebMvcTest({EventFormatter.class, EventController.class})
public class EventControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void getTest() throws Exception {
        mockMvc.perform(get("/event/1"))
                .andExpect(status().isOk())
                .andExpect(content().string("1"));
    }
}

```
![image](https://user-images.githubusercontent.com/63777714/141683527-74997bed-8e1d-4714-a4e1-6137afc53f7a.png)

정상적으로 잘 작동한 것을 볼 수 있습니다. 

---

참고한 사이트
* https://engkimbs.tistory.com/738

