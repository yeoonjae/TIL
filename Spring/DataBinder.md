> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ํต์ฌ ๊ธฐ์ '์ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>


# **DataBinder ์ถ์ํ** 
  ๋ฐ์ดํฐ ๋ฐ์ธ๋ฉ์ด๋ ๊ธฐ์ ์ ์ธ ๊ด์ ์์ ๋ดค์ ๋ Property ์ ๊ฐ์ target ๊ฐ์ฒด์ ์ค์ ํ๋ ๊ธฐ๋ฅ์ด๋ฉฐ, ์ฌ์ฉ์์ ๊ด์ ์์ ๋ดค์ ๋ ์ฌ์ฉ์ ์๋ ฅ๊ฐ์ ์ ํ๋ฆฌ์ผ์ด์ ๋๋ฉ์ธ ๋ชจ๋ธ์ ๋์ ์ผ๋ก ๋ณํํ์ฌ ๋ฃ์ด์ฃผ๋ ๊ธฐ๋ฅ์๋๋ค. 

  ์๋ฅผ ๋ค์ด, ์ฌ์ฉ์๋ "12345"์ด๋ผ๋ ๋ฌธ์์ด์ ์๋ ฅํฉ๋๋ค. ์ด "12345"์ด๋ผ๋ ๊ฐ์ int, long ๋๋ User ๋ฑ ๋ค์ํ ๋๋ฉ์ธ ํ์์ผ๋ก ๋ณํํด์ ๋ฃ์ด์ฃผ๋ ๊ธฐ๋ฅ์ ๋งํฉ๋๋ค. 

์คํ๋ง ์ด์ฐฝ๊ธฐ์๋ PropertyEditor ์ธํฐํ์ด์ค๋ฅผ ์ฌ์ฉํ์ฌ ๋ฐ์ดํฐ ๋ฐ์ธ๋ฉ์ ํ์์ง๋ง, ํ์ ์ด๋ฅผ ๋์ฒดํ  Converter์ Formatter๋ ๋ฐ์ดํฐ ๋ฐ์ธ๋ฉ ์ธํฐํ์ด์ค๊ฐ ๋์ค๊ฒ ๋์์ต๋๋ค. 

Converter๋ Sํ์์ T ํ์์ผ๋ก ๋ณํํ  ์ ์๋ ์ผ๋ฐ์ ์ธ ๋ณํ๊ธฐ์ด๋ฉฐ ์ฐ๋ ๋์ธ์ดํํฉ๋๋ค. Formatter๋ Object์ String๊ฐ์ ๋ณํ์ ๋ด๋นํ๋ฉฐ ๋ฌธ์์ด์ Locale์ ๋ฐ๋ผ ๋ค๊ตญํํ๋ ๊ธฐ๋ฅ์ ์ ๊ณตํฉ๋๋ค. ์น ์ดํ๋ฆฌ์ผ์ด์์ ์ ์ํ  ๋๋ ์ฃผ๋ก Formatter๋ฅผ ์ฌ์ฉํฉ๋๋ค.


---
  ## **Converter**
  * Converter๋ ์ ๋ค๋ฆญ์ผ๋ก ๋ ๊ฐ์ ์ธ์, Source์ Target์ ๋ฐ์์ ๊ตฌํํฉ๋๋ค. 
  * Sํ์์ T ํ์์ผ๋ก ๋ณํํ  ์ ์๋ ์ผ๋ฐ์ ์ธ ๋ณํ๊ธฐ์๋๋ค. 
  * Converter๋ PropertyEditor์๋ ๋ค๋ฅด๊ฒ Thread-safeํ๊ธฐ ๋๋ฌธ์ ์คํ๋ง ๋น์ผ๋ก ๋ฑ๋กํด์ ์ฌ์ฉ ๊ฐ๋ฅํฉ๋๋ค.

### **Converter ์ค์ต์ฝ๋**

 ```java
 package me.whiteship.dempspring51;

import org.springframework.core.convert.converter.Converter;
import org.springframework.stereotype.Component;

public class EventConverter {

    // converter๋ ์์ค์ ํ๊ฒ ๋๊ฐ์ ์ธ์๋ฅผ ๋ฐ๋๋ค ์ด ํด๋์ค์ ๊ฒฝ์ฐ Stringํ์์ ์์ค๋ก ๋ฐ์ Event ํ์์ผ๋ก ๋ณํ์ํค๋ ๊ฒ์ด๋ค.
    public static class StringToEventConverter implements Converter<String, Event> {

        @Override
        public Event convert(String source) {
            // ์์ค(String)์ ๋ฐ์์ Event๋ก ๋ณํํ๋ฉด ๋๋ค. 
            return new Event(Integer.parseInt(source));
        }

    }

    // converter๋ ์์ค์ ํ๊ฒ ๋๊ฐ์ ์ธ์๋ฅผ ๋ฐ๋๋ค ์ด ํด๋์ค์ ๊ฒฝ์ฐ Event ํ์์ ์์ค๋ก ๋ฐ์ String ํ์์ผ๋ก ๋ณํ์ํค๋ ๊ฒ์ด๋ค.
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
        // registry์ addConverter ๋ก EventConerterํด๋์ค์์ ๋ง๋  StringToEventConverter๋ฅผ ๋ฑ๋กํด์ค๋๋ค. 
        registry.addConverter(new StringToEventConverter());
    }
}
```
 - Converter๋ฅผ ๋ฑ๋กํ๋ ค๋ฉด Spring MVC ์์๋ `WebMvcConfigurer` ์ธํฐํ์ด์ค๋ฅผ ๊ตฌํํ 
 ํด๋์ค๋ฅผ ์์ฑํด์ผํฉ๋๋ค. `addFormatters` ๋ฉ์๋๋ฅผ ์ค๋ฒ๋ผ์ด๋ฉํ์ฌ ๋ฑ๋กํฉ๋๋ค. 
 

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
* WebConfig ํด๋์ค์ ๋ฃ์ด์ค Converter๊ฐ ๋ชจ๋  Controller์ ๋์ํฉ๋๋ค. 
* ๋ฐ๋ผ์ EventControllerTest์์ ์์ฒญํ 1 ์ด Converter์์ Event๋ก ๋ณํ์ด ๋์ด, Event Type์ผ๋ก ๋ฐ์ ์ ์์ต๋๋ค. 

EventControllerTest๋ฅผ ์คํํ ๊ฒฐ๊ณผ๋ ๋ค์๊ณผ ๊ฐ์ต๋๋ค. 
   
```java 
Event{id=1, title='null'}
```

### **์ ๋ฆฌ**
  * S ํ์์ T ํ์์ผ๋ก ๋ณํํ  ์ ์๋ ์ผ๋ฐ์ ์ธ ๋ณํ๊ธฐ
  * ์ํ ์ ๋ณด ์์ == `Stateless` == ์ฐ๋ ๋ ์ธ์ดํ
  * `ConverterRegistry`์ ๋ฑ๋กํ์ฌ ์ฌ์ฉ
  * `Converter<String, Event>` ๋ก ์ฌ์ฉ๋๋ฉด `String` ํ์์ `source`๋ก ๋ฐ์ `Event` `Type`์ผ๋ก ๋ณํํ๋ค๋ ๊ฒ์๋๋ค. 


---
## **Fomatter**
 * Formatter๋ ์ ๋ค๋ฆญ์ผ๋ก ํ ๊ฐ์ ์ธ์๋ง์ ๋ฐ์ต๋๋ค. 
 * **Object์ String๊ฐ์ ๋ณํ์ ๋ด๋นํ๊ธฐ ๋๋ฌธ์ ๋ค๋ฅธ ํ๋์ ์ธ์๋ String์ผ๋ก ์ ํด์ ธ ์๊ธฐ ๋๋ฌธ**์๋๋ค. 


 Formatter๋ `parse(String text, Locale locale)` , `print(๋ณํํ  ๊ฐ์ฒด object, Locale locale)` ์ด ๋๊ฐ์ง ๋ฉ์๋๋ฅผ ์ค๋ฒ๋ผ์ด๋ฉ ํด์ผํฉ๋๋ค. 

 ๋ํ ์ํ๋ฅผ ๊ฐ๊ณ ์์ง ์์์ **<u>์ค๋ ๋์ธ์ดํ</u>** ํฉ๋๋ค. ๋ฐ๋ผ์ `@Component` ์ด๋ธํ์ด์์ ๋ถ์ฌ Bean์ผ๋ก ๋ฑ๋ก์ด ๊ฐ๋ฅํฉ๋๋ค. 

 ### **Fomatter ์ค์ต์ฝ๋**
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
//        registry.addConverter(new StringToEventConverter()); // Converter ๋ฑ๋ก๋ฐฉ๋ฒ
        registry.addFormatter(new EventFormatter());
    }
}

```

ํ์คํธ ๊ฒฐ๊ณผ๋ ๋ค์๊ณผ ๊ฐ์ต๋๋ค. 

```java
Event{id=1, title='null'}
```

### **์ ๋ฆฌ**
 * PropertyEditor ๋์ฒด์ ๋ก ์ฌ์ฉ
 * Object์ String ๊ฐ์ ๋ณํ์ ๋ด๋น
 * ๋ฌธ์์ด์ Locale์ ๋ฐ๋ผ ๋ค๊ตญํ ํ๋ ๊ธฐ๋ฅ๋ ์ ๊ณต. (optional)
 * FormatterReguistry์ ๋ฑ๋กํ์ฌ ์ฌ์ฉ

---

 ## **ConversionService**
<br>

![image](https://user-images.githubusercontent.com/63777714/141680785-8c29f74d-80bb-42ed-b497-e0eb92a47664.png)

`ConversionService`๋ ์ค์  ๋ฐ์ดํฐ ๋ณํ์ด ์ผ์ด๋๋ ๊ณณ์๋๋ค. `Converter`์ `formatter` ๋ํ SpringMVC์ ๊ฒฝ์ฐ `WebMvcConfigurer`๋ฅผ ํตํด `ConversionService`์ ๋ฑ๋ก์ด ๋๋ฉฐ  `ConversionService`์์ ์ค์  ๋ฐ์ดํฐ ๋ณํ์ด ์ผ์ด๋ฉ๋๋ค. 

SpringBoot์์ `ConversionService`๋ฅผ ์ฌ์ฉํ๋ ์ฝ๋๋ฅผ ์์ฑํด๋ณด๊ฒ ์ต๋๋ค. 

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


์ ํ์ผ์ ์คํ ๊ฒฐ๊ณผ๋ ๋ค์๊ณผ ๊ฐ์ต๋๋ค. 

```java
class org.springframework.boot.autoconfigure.web.format.WebConversionService
```

`ConversionService`๋ `DefaultFormattingConversionService`๊ฐ ๋์ค๋ ๊ฒ์ด ์๋๋ผ `WebConversionService`๊ฐ ๋์จ ๊ฒ์ ํ์ธํ  ์ ์์ต๋๋ค. 

๐คทโโ๏ธ ์ด์งธ์ ์ด๋ฐ ๊ฒฐ๊ณผ๊ฐ ๋์ค๋ ๊ฒ์ผ๊น์? 

> ๋ฐ๋ก Spring-Boot ํ๊ฒฝ์์ ์คํํ๊ธฐ ๋๋ฌธ์๋๋ค. `WebConversionService`๋ Spring-Boot์์ ์ ๊ณตํด์ฃผ๋ ํด๋์ค๋ก `DefaultFormattingConversionService`๋ฅผ ์์ํ์ฌ ๋ง๋  ๊ฒ์ผ๋ก ๋น์ผ๋ก ๋ฑ๋กํด์ค๋๋ค. <br><br>
> ๋ํ, Spring-Boot์์๋ WebConfig ํด๋์ค ํ์ผ์ด ์์ด๋ (Registry๋ฅผ ์ฌ์ฉํ์ง ์์๋) ์ด๋ธํ์ด์์ ํตํด Bean์ผ๋ก ๋ฑ๋กํด์ค๋ค๋ฉด Formatter์ Converter Bean์ ์ฐพ์์ ์๋์ผ๋ก ๋ฑ๋กํด์ค๋๋ค. 

---
 ## **WebConversionService**
* Spring-Boot ๊ฐ ์ ๊ณตํด์ฃผ๋ ํด๋์ค๋ก `DefaultFormattingConversionService`๋ฅผ ์์๋ฐ์ ๊ตฌํ๋ ํด๋์ค์๋๋ค. ๋น์ผ๋ก ๋ฑ๋ก๋ `Formatter`์ `Converter`๋ฅผ ์๋์ผ๋ก ๋ฑ๋กํด์ฃผ์ด `WebConversionService` ์ธํฐํ์ด์ค๋ฅผ ๊ตฌํํ  ํ์๊ฐ ์์ต๋๋ค. 

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

Test ์ฝ๋์ธ ๊ฒฝ์ฐ
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

์ ์์ ์ผ๋ก ์ ์๋ํ ๊ฒ์ ๋ณผ ์ ์์ต๋๋ค. 

---

์ฐธ๊ณ ํ ์ฌ์ดํธ
* https://engkimbs.tistory.com/738

