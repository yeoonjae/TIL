> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **스프링 MVC 활용**

## **1. HTTP 요청 맵핑하기**

HTTP Method 종류는 `GET`, `POST`, `PUT`, `PATCH`, `DELETE` 이 대표적으로 있습니다. 
적용 예제와 각 Method의 특징을 살펴보도록 하겠습니다. 

### **예제코드**
```java
@Controller
public class SampleController {

    @RequestMapping(value = "/hello", method = RequestMethod.GET)
    @ResponseBody
    public String hello() {
        return "hello";
    }
}
```
Controller로 정의된 클래스에서 `@RequestMapping` 어노테이션을 달면 `value`에 해당하는 url을 처리하겠다는 의미입니다. `method`는 들어오는 HTTP Method를 정의하는 부분입니다. 만약 `method`를 지정하지 않으면 모든 HTTP Method를 적용할 수 있고, 배열을 통해 여러개의 HTTP Mehtod를 적용시킬 수 있습니다. 


### **GET 요청**
* 클라이언트가 서버의 리소스를 요청할 때 사용합니다. 
* 캐싱 할 수 있습니다. (조건적인 GET으로 바뀔 수 있습니다.)
    * 응답을 보낼 때 캐시와 관련된 헤더를 응답에 실어서 보낼 수 있습니다. 
* 브라우저 기록에 남습니다.
* 북마크 할 수 있습니다.
* URL에 노출되므로 민감한 데이터를 보낼 때는 사용하지 않아야 합니다. 
* idempotent : 동일한 get 요청은 항상 동일한 응답을 응답해야 합니다. 

### **POST 요청**
* 클라이언트가 서버의 리소스를 수정하거나 새로 만들 때 사용합니다.
    * idempotent하지 않습니다. 동일한 post 요청을 보내더라도 이에 대한 응답은 매번 달라질 수 있습니다. 
* 서버에 보내는 데이터를 POST 요청 본문에 담아서 보냅니다.
* 캐시할 수 없습니다.
* 브라우저 기록에 남지 않습니다.
* 북마크 할 수 없습니다.
* 데이터 길이 제한이 없습니다.

### **PUT 요청**
* URI에 해당하는 데이터를 새로 만들거나 수정할 때 사용합니다. 
* POST와 다른 점은 `URI`에 대한 의미가 다릅니다.
    * `POST의 URI는 보내는 데이터를 처리할 리소스를 지칭`하며
    * `PUT의 URI는 보내는 데이터에 해당하는 리소스를 지칭`한다. (리소스 자체를 지칭합니다.)
* Idempotent 한 특징을 가집니다. 

> 🕵🏻‍♂️ POST와 PUT 요청 둘 다 어떤 리소스를 수정하거나 생성할 때 사용할 수 있지만
> * `POST`는 URI가 데이터를 처리할 리소스를 지칭하며, 요청을 보냈을 때의 응답이 매번 달라질 수 있습니다. 
> * `PUT`은 URI가 보내는 데이터 자체에 해당하는 리소스르 지칭하며, 요청을 보냈을 때 응답이 매번 동일합니다. (`Idempotent`)

### **PATCH 요청**
* PUT과 비슷하지만, 기존 엔티티와 새 데이터의 차이점만 보낸다는 차이가 있습니다. 
* 즉, 서버에 존재하는 데이터와 새로 업데이트 된 데이터의 차이점만 보냅니다. 
* Idempotent 한 특징을 가집니다. 

### **DELETE 요청**
* URI에 해당하는 리소스를 삭제할 때 사용한다.
* Idempotent 한 특징을 가집니다. 

### **스프링 웹 MVC에서 HTTP method 맵핑하기**
* @RequestMapping(method=RequestMethod.GET)
* @RequestMapping(method={RequestMethod.GET, RequestMethod.POST})
* @GetMapping, @PostMapping,..

## **2. URI 패턴 맵핑하기**

> 🕵🏻‍♂️ URI? URL? 뭐가 다른가요? <br>
> URI에 URL이 속해있는 것으로 모든 URL은 URI라고 말할 수 있습니다. <br>
> * **URI(Uniform resource Identifier)** 네트워크 상에서 자원 위치를 알려주기 위한 규약입니다. 
> * **URI**의 존재는 인터넷에서 요구되는 기본조건으로서 인터넷 프로토콜에 항상 붙어 다닙니다.
> * **URL(Uniform Resource Locator)** 통합 자원 식별자로 인터넷에 있는 자원을 나타내는 유일한 주소입니다.
> * 즉, **URI가 URL의 상위 개념** (URL이 URI안에 포함 되어있다고 생각하면 될것 같습니다.
URI 의 하위 개념으로는 URL 말고 URN도 있습니다.)


### **요청 식별자로 맵핑하기**
* `@RequestMapping`은 다음의 패턴을 지원합니다.
* ?: 한 글자 (“/author/???” => “/author/123”)
* *: 여러 글자 (“/author/*” => “/author/keesun”)
* ** : 여러 패스 (“/author/** => “/author/keesun/book”)

### **클래스에 선언한 @RequestMapping과 조합**
* 클래스에 선언한 URI 패턴뒤에 이어 붙여서 맵핑합니다.

```java
@Controller
@RequestMapping(value = "/hello")
public class SampleController {

    @ResponseBody
    @RequestMapping("/**")
    public String hello() {
        return "hello";
    }
}
```
다음과 같이 선언하면 클래스에 선언한 `@RequestMapping`과 메소드에 선언한 `@RequestMapping`이 이어붙여서 맵핑합니다. 그러므로 다음의 테스트는 통과합니다. 

```java
@ExtendWith(MockitoExtension.class)
@WebMvcTest
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void helloTest() throws Exception{
        mockMvc.perform(get("/hello/yeonjae/123"))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(content().string("hello"));
    }
}
```
### **정규 표현식으로 맵핑**
* /`{name:정규식}`

### **패턴이 중복되는 경우에는?**
* 가장 구체적으로 맵핑되는 핸들러를 선택합니다.

```java
@Controller
@RequestMapping(value = "/hello")
public class SampleController {

    @ResponseBody // 이 메소드를 맵핑함
    @RequestMapping("/yeonjae")
    public String hello(@PathVariable String name) {
        return "hello " + name;
    }

    @ResponseBody
    @RequestMapping("/**")
    public String helloYeonjae(@PathVariable String name) {
        return "hello " + name;
    }
}

```

### **URI 확장자 맵핑 지원**
* 이 기능은 권장하지 않습니다. (*스프링 부트에서는 기본으로 이 기능을 사용하지 않도록
설정* 해 줍니다.) 
    * 보안 이슈 (RFD Attack)
    * URI 변수, Path 매개변수, URI 인코딩을 사용할 때 할 때 불명확 함. 
* 다양한 응답을 지원하려면 accept header 에 header에 json, html..등을 설정해서 보내는 것을 권장합니다. 
* 또는 parameter를 통해 `@RequestMapping(value = "/hello?tyupe=xml")` 이와 같이 설정할 수도 있습니다. 

## **3. 컨텐츠 타입 맵핑하기**

```java
@Controller
public class SampleController {

    @ResponseBody
    @RequestMapping(value = "/hello",consumes = "application/json")
    public String hello() {
        return "hello";
    }

}
```
```java
@ExtendWith(MockitoExtension.class)
@WebMvcTest
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void helloTest() throws Exception{
        mockMvc.perform(get("/hello")
                .contentType(MediaType.APPLICATION_JSON))
            .andDo(print())
            .andExpect(status().isOk())
            .andExpect(content().string("hello"));

    }

}
```
### **특정한 타입의 데이터를 담고 있는 요청만 처리하는 핸들러**
* `@RequestMapping(consumes = "application/json")` - json 요청만 처리하는 핸들러
* Content-Type 헤더로 필터링합니다. 
* 매치 되는 않는 경우에 415 Unsupported Media Type 응답하빈다. 

### **특정한 타입의 응답을 만드는 핸들러**
* `@RequestMapping(produces=”application/json”)` - json 응답만 받겠다는 핸드러 
* Accept 헤더로 필터링 (하지만 살짝... 오묘함) 
    - accept 헤더를 설정하지 않으면 맵핑 성공하게 됩니다. 
* 매치 되지 않는 경우에 406 Not Acceptable 응답합니다. 

### **추가설정**
* 문자열을 입력하는 대신 MediaType을 사용하면 상수를 (IDE에서) 자동 완성으로 사용할 수 있습니다.
* 클래스에 선언한 @RequestMapping에 사용한 것과 조합이 되지 않고
* 메소드에 사용한 @RequestMapping의 설정으로 덮어씁니다.
* Not (!)을 사용해서 특정 미디어 타입이 아닌 경우로 맵핑 할 수도 있습니다.

## **4. HEAD와  OPTIONS 요청 처리하기**

우리가 구현하지 않아도 `스프링 웹 MVC에서 자동으로 처리`하는 HTTP Method 중
* HEAD
* OPTIONS

가 존재합니다. 

### **HEAD**
* GET 요청과 동일하지만 응답 본문을 받아오지 않고 응답 헤더만 받아옵니다. (body는 받아오지 않고 header만 받아옴)

### **OPTIONS**
* 사용할 수 있는 HTTP Method 제공합니다.
* 서버 또는 특정 리소스가 제공하는 기능을 확인할 수 있습니다.
* 서버는 Allow 응답 헤더에 사용할 수 있는 HTTP Method 목록을 제공해야 합니다.

```java
    @Test
    public void helloTest() throws Exception{
        mockMvc.perform(options("/hello"))
            .andDo(print())
            .andExpect(status().isOk());


    }
```

![image](https://user-images.githubusercontent.com/63777714/144735839-d6bd5ac0-49e0-4b85-9c22-b25255b16efa.png)

options에 대한 method 설정을 별도로 하지 않아도 options 로 요청을 보내면 위의 사진과 같이 사용할 수 있는 HTTP Method 목록을 반환해줍니다.

**참고**
* https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html
* https://github.com/spring-projects/spring-framework/blob/master/spring-test/src/test/java/org/springframework/test/web/servlet/samples/standalone/resultmatchers/HeaderAssertionTests.java


## **5. 커스텀 어노테이션**
`@RequestMapping` **애노테이션을 메타 애노테이션으로 사용하기**
* @GetMapping 같은 커스텀한 애노테이션을 만들 수 있습니다. 

**메타(Meta) 애노테이션**
* 애노테이션에 사용할 수 있는 애노테이션
* 스프링이 제공하는 대부분의 애노테이션은 메타 애노테이션으로 사용할 수 있습니다.

**조합(Composed) 애노테이션**
* 한개 혹은 여러 메타 애노테이션을 조합해서 만든 애노테이션
* 코드를 간결하게 줄일 수 있습니다.
* 보다 구체적인 의미를 부여할 수 있습니다.

**@Retention**
* 해당 애노테이션 정보를 언제까지 유지할 것인가를 결정합니다.
* Source: 소스 코드까지만 유지. 즉, 컴파일 하면 해당 애노테이션 정보는 사라진다는 이야기입니다.
* Class: 컴파인 한 .class 파일에도 유지. 즉 런타임 시, 클래스를 메모리로 읽어오면 해당 정보는 사라집니다.
* Runtime: 클래스를 메모리에 읽어왔을 때까지 유지! 코드에서 이 정보를 바탕으로 특정 로직을 실행할 수 있습니다.

**@Target**
* 해당 애노테이션을 어디에 사용할 수 있는지 결정합니다. 

**@Documented**
* 해당 애노테이션을 사용한 코드의 문서에 그 애노테이션에 대한 정보를 표기할지 결정합니다. 

![image](https://user-images.githubusercontent.com/63777714/144736565-3d374e11-1929-4c10-b205-fa3e5a3741e1.png)

위 그림은 `GetHelloMapping` 이라는 커스텀 어노테이션을 생성한 것입니다. 각 설정을 보면, `@Retention` 설정을 통해 Runtime 시에도 해당 어노테이션을 실행할 수 있게끔 설정을 하였으며, `@Target`을 설정해주어 Method 에 사용한다고 명시해주었습니다. 이처럼 어노테이션을 직접 만들어 사용하는 방법을 커스텀 어노테이션이라 부릅니다. 


