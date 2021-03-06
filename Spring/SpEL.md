> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ํต์ฌ ๊ธฐ์ '์ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>


# **Spring Expression Language : SpEL** 
**SpEL**์ **Spring Expression Language**์ ์ฝ์๋ก ๋ฐํ์ ์์ ๊ฐ์ฒด ๊ทธ๋ํ๋ฅผ ์กฐํํ๊ณ  ์กฐ์ํ๋ **๊ฐ๋ ฅํ ํํ์ธ์ด**์๋๋ค. Unified EL๊ณผ ๋น์ทํ์ง๋ง, ๋ฉ์๋ ํธ์ถ์ ์ง์ํ๋ฉฐ, ๋ฌธ์์ด ํํ๋ฆฟ ๊ธฐ๋ฅ๋ ์ ๊ณตํฉ๋๋ค. 
 
---
##  **SpEL ๊ตฌ์ฑ**
* ExpressionParser parser = new SpelExpressionParser()
* StandardEvaluationContext context = new StandardEvaluationContext(bean)
* Expression expression = parser.parseExpression(โSpEL ํํ์โ)
* String value = expression.getvalue(context, String.class)

---
## **ExpressionParser**
SpEL์ ๋ด๋ถ์ ์ผ๋ก ExpressionParser ๊ฐ์ฒด๋ฅผ ํตํด SpEL์ ํํ์์ ํ์ฑํ๋ฉฐ, StandardEvaluationContext ๊ฐ์ฒด๋ฅผ ํตํด ์คํ๋ง ๋น์ด ๊ฐ์ง๊ณ  ์๋ ๊ฐ์ฒด ์ ๋ณด๋ฅผ ๊ตฌํฉ๋๋ค. 

## **@Value ์ SpE**
* ๋ฌธ๋ฒ
    * #{โํํ์"}
    * ${โํ๋กํผํฐ"}
    * ํํ์์ ํ๋กํผํฐ๋ฅผ ๊ฐ์ง ์ ์์ง๋ง, ๋ฐ๋๋ ์ ๋จ.
        * #{${my.data} + 1}

์๋์ ์ฝ๋๋ `@Value` ์ด๋ธํ์ด์๊ณผ ๊ฐ์ด ์ฐ์ด๋ ๊ฒฝ์ฐ์ `ExpressionParser`๋ฅผ ์ฌ์ฉํ ๊ฒฝ์ฐ๋ฅผ ๋ํ๋ธ ๊ฒ์ด๋ค. 
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

    @Value("${my.value}") // ํ๋กํผํฐ ๊ฐ ์ฝ์ด์ค๊ธฐ
    String myName;

    @Value("#{${my.value} eq 100}") // ํ๋กํผํฐ ๊ฐ๊ณผ์ ๋น๊ต ๊ฐ๋ฅ
    boolean isMyNameSaelobi;

    @Value("#{sample.data}") // Bean์ผ๋ก ๋ฑ๋ก๋ ๊ฐ์ฒด ์ฐธ์กฐ ๊ฐ๋ฅ
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
## **์ค์ ๋ก ์ด๋์ ์ฐ์ด๋**
* @Value ์ ๋ธํ์ด์
* @ConditionalOnExpression ์ ๋ธํ์ด์
* ์คํ๋ง ์ํ๋ฆฌํฐ
    * ๋ฉ์๋ ์ํ๋ฆฌํฐ, @PreAuthorize, @PostAuthorize, @PreFilter, @PostFilter
    * XML ์ธํฐ์ํฐ URL ์ค์  ...
* ์คํ๋ง ๋ฐ์ดํฐ
    * @Query ์ ๋ธํ์ด์
* Thymeleaf

---
์ฐธ๊ณ ํ ์ฌ์ดํธ
* https://engkimbs.tistory.com/741?category=767795