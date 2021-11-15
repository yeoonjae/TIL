> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>


# **Spring Expression Language : SpEL** 
**SpEL**은 **Spring Expression Language**의 약자로 런타임 시에 객체 그래프를 조회하고 조작하는 **강력한 표현언어**입니다. Unified EL과 비슷하지만, 메소드 호출을 지원하며, 문자열 템플릿 기능도 제공합니다. 
 
---
##  **SpEL 구성**
* ExpressionParser parser = new SpelExpressionParser()
* StandardEvaluationContext context = new StandardEvaluationContext(bean)
* Expression expression = parser.parseExpression(“SpEL 표현식”)
* String value = expression.getvalue(context, String.class)

---
## **ExpressionParser**
SpEL은 내부적으로 ExpressionParser 객체를 통해 SpEL의 표현식을 파싱하며, StandardEvaluationContext 객체를 통해 스프링 빈이 가지고 있는 객체 정보를 구합니다. 

## **@Value 와 SpE**
* 문법
    * #{“표현식"}
    * ${“프로퍼티"}
    * 표현식은 프로퍼티를 가질 수 있지만, 반대는 안 됨.
        * #{${my.data} + 1}

아래의 코드는 `@Value` 어노테이션과 같이 쓰이는 경우와 `ExpressionParser`를 사용한 경우를 나타낸 것이다. 
```java
#application.properties
my.value=100
```
```java
@Component
public class Sample {

    private int data = 200;

    public int getData() {
        return data;
    }

    public void setData(int data) {
        this.data = data;
    }
}
```
```java
@Component
public class AppRunner implements ApplicationRunner {


    @Value("#{1+1}")
    int value;

    @Value("#{'Hello ' + ' World'}")
    String hello;

    @Value("#{1 eq 1}")
    boolean trueOrFalse;

    @Value("you are great")
    String great;

    @Value("${my.value}") // 프로퍼티 값 읽어오기
    String myName;

    @Value("#{${my.value} eq 100}") // 프로퍼티 값과의 비교 가능
    boolean isMyNameSaelobi;

    @Value("#{sample.data}") // Bean으로 등록된 객체 참조 가능
    int sampleData;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("==============");
        System.out.println(value);
        System.out.println(hello);
        System.out.println(trueOrFalse);
        System.out.println(great);
        System.out.println(myName);
        System.out.println(isMyNameSaelobi);
        System.out.println(sampleData);

        ExpressionParser parser = new SpelExpressionParser();
        Expression expression = parser.parseExpression("2+100");
        Integer value = expression.getValue(Integer.class);
        System.out.println(value);
    }
}

```

![image](https://user-images.githubusercontent.com/63777714/141694124-5bde1d6b-d712-4a63-a3e2-98bcf3dc100c.png)


---
## **실제로 어디서 쓰이나**
* @Value 애노테이션
* @ConditionalOnExpression 애노테이션
* 스프링 시큐리티
    * 메소드 시큐리티, @PreAuthorize, @PostAuthorize, @PreFilter, @PostFilter
    * XML 인터셉터 URL 설정 ...
* 스프링 데이터
    * @Query 애노테이션
* Thymeleaf

---
참고한 사이트
* https://engkimbs.tistory.com/741?category=767795