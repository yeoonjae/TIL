> π λ³Έ κΈμ μΈνλ° κ°μ 'μ€νλ§ ν΅μ¬ κΈ°μ 'μ λ£κ³  μ λ¦¬ν κΈμλλ€. 

<br>

# **Environment**


`ApplicationContext` ν΄λμ€λ `EnvironmentCapable`μ μμλ°κ³  μμ΅λλ€. μ΄ `EnvironmentCapable`μ΄ μ κ³΅νλ κΈ°λ₯λ€μ μ΄ν΄λ³΄λλ‘ νκ² μ΅λλ€. 

---

## **1. νλ‘νμΌ(Profile)**
 * λΉλ€μ λ¬Άμ
 * Environmentμ μ­ν μ νμ±νν  νλ‘νμΌ νμΈ λ° μ€μ 
 * μ΄λ€ νΉμ  νκ²½μμλ§ λΉλ€μ΄ λ±λ‘λμ΄μΌ νλ κ²½μ°λ₯Ό μΆ©μ‘±μν€κΈ° μν΄ λ±μ₯
 
---
### 1.1 νλ‘νμΌ μ μ¦μΌμ΄μ€
*  νμ€νΈ νκ²½μμλ AλΌλ λΉμ μ¬μ©νκ³ , λ°°ν¬ νκ²½μμλ BλΌλ λΉμ μ°κ³  μΆλ€.
*  μ΄ λΉμ λͺ¨λν°λ§ μ©λλκΉ νμ€νΈν  λλ νμκ° μκ³  λ°°ν¬ν  λλ§ λ±λ‘μ΄ λλ©΄
μ’κ² λ€.

μμ κ²½μ°μ ν΄λΉν  λ μ¬μ©ν©λλ€. 

---
### 1.2 νλ‘νμΌ μ μνκΈ°
1. ν΄λμ€μ μ μ
   * @Configuration @Profile(βtestβ)
   * @Component @Profile(βtestβ)

(μμ μμ : test νκ²½μμλ§ μ μ©μ΄ λμ΄μΌ λ  λ )

```java
@Configuration
@Profile("test")
public class TestConfiguration {

    @Bean
    public BookRepository bookRepository(){
        return new TestBookRepository();
    };
}

// λλ ComponentScanμΌλ‘ λ±λ‘λλ λΉμλ νλ‘νμΌμ μ§μ ν  μ μμ΅λλ€. 

@Repository
@Profile("test")
public class TestBookRepository implements BookRepository {

}
```

2. λ©μλμ μ μ
   * @Bean @Profile(βtestβ)

```java
@Configuration
public class TestConfiguration {

    @Bean
    @Profile("test")
    public BookRepository bookRepository(){
        return new TestBookRepository();
    };
}
```

---

### 1.3 νλ‘νμΌ μ€μ νκΈ°
* -Dspring.profiles.avtive=βtest,A,B,...β
*  @ActiveProfiles (νμ€νΈμ©)

![image](https://user-images.githubusercontent.com/63777714/139589848-adbe701b-0084-420e-9a78-cac6237a0100.png)

 * νλ‘νμΌ μ€μ νλ λ°©λ²μ μ°μΈ‘ μλ¨μ λΉ¨κ° λ°μ€λ₯Ό ν΄λ¦­νμλ©΄ edit Configuration μ΄λΌλ λ©λ΄κ° μμ΅λλ€. κ·Έ λ©λ΄λ₯Ό ν΄λ¦­νμλ©΄ κ·Έλ¦Όμ²λΌ λ³΄μ΄λ μ°½μ΄ λ¨λλ°, λΉ¨κ°μ λ°μ€λ‘ νμν΄λμ Active profile μ 'test' λ₯Ό μλ ₯νκ² λλ©΄ testμ νλ‘νμΌμ΄ μ μ©μ΄ λ©λλ€. 

 * λλ Active profile μ΄λΌλ μ΅μμ΄ μλ κ²½μ°μλ Environment μμ μλ VM optionsμ `Dspring.profiles.avtive=βtest"` λΌλ μ€μ μ μ£Όμ΄ test νλ‘νμΌλ‘ μ μ©μν€κ²λ κ°λ₯ν©λλ€. 

```java
// μ€νμ½λ
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Autowired
    BookRepository bookRepository;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
        System.out.println(Arrays.toString(environment.getDefaultProfiles()));
    }
}
```
μ€νκ²°κ³Ό

      [test]
      [default]


---
### 1.4 νλ‘νμΌ ννμ
 * ! (not)
 * & (and)
 * | (or)

       ex. `@Profile(" ! test")` : test κ° μλλλΌλ λ»

 ---
 ## **2. νλ‘νΌν°(Property)**
 μ΄νλ¦¬μΌμ΄μμ λ±λ‘λμ΄ μλ Key-value μμΌλ‘ μ κ³΅λλ property μ μ κ·Όν  μ μλ κΈ°λ₯μλλ€. 
  * λ€μν λ°©λ²μΌλ‘ μ μν  μ μλ μ€μ κ°
  * Environment μ μ­ν μ νλ‘νΌν° μμ€ μ€μ  λ° νλ‘νΌν° κ° κ°μ Έμ€κΈ°
  * νλ‘νΌν°λ μ°μ  μμκ° μμ΅λλ€. 
     *  ServletConfig λ§€κ°λ³μ
     *  ServletContext λ§€κ°λ³μ
     *  JNDI (java:comp/env/)
     *  JVM μμ€ν νλ‘νΌν° (-Dkey=βvalueβ)
     * JVM μμ€ν νκ²½ λ³μ (μ΄μ μ²΄μ  νκ²½ λ³μ)

### 2.1 νλ‘νΌν° μ€μ νκΈ° 
1. VM-optionsλ₯Ό μ΄μ©ν μ€μ 

![image](https://user-images.githubusercontent.com/63777714/139590746-99524fc9-5d6c-4ca0-b893-08acac6d64fa.png)

μμ κ·Έλ¦Όκ³Ό κ°μ΄ VM optionsμ `-Dapp.name=spring5` μ²λΌ μ€μ μ ν  μ μμ΅λλ€. 

μ€νμ½λ 
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Autowired
    BookRepository bookRepository;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        // environmentμ getPropertyλ‘ appμ name κΊΌλ΄μ€κΈ° 
        System.out.println(environment.getProperty("app.name"));
    }
}

//  μ€νκ²°κ³Ό
spring5
```

<br>

2. properties νμΌ μΆκ° ν `@PropertySource` λ₯Ό ν΅ν μ€μ 

![image](https://user-images.githubusercontent.com/63777714/139591254-f0c0f3f2-86a8-429c-b212-c0e40fde7c78.png)

resouces μ app.properties νμΌμ νλ μμ± ν 

    app.about=spring

λ€μκ³Ό κ°μ΄ μμ±ν΄μ£Όμμ΅λλ€. 

κ·Έλ¦¬κ³  @Configurationμ΄ λ€μ΄μλ ν΄λμ€μμ `@PropertySource("classpath:/app.properties")` λ€μκ³Ό κ°μ΄ μ μ΄μ£Όλ©΄ properties λ±λ‘ λ° μ€μ μ΄ μ μ©λλ κ²μ νμΈν  μ μμ΅λλ€. 

```java
// configuration μ΄ λ€μ΄μλ ν΄λμ€ 
@SpringBootApplication
@PropertySource("classpath:/app.properties")
public class Dempspring51Application {

    public static void main(String[] args) {
        SpringApplication.run(Dempspring51Application.class, args);
    }
}

// μ€νμ½λ
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Autowired
    BookRepository bookRepository;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(environment.getProperty("app.name"));
        System.out.println(environment.getProperty("app.about"));
    }
}

// μ€νκ²°κ³Ό
spring5
spring
```

> π³κ·Έλ λ€λ©΄ VM options κ³Ό PropertySourceλ₯Ό ν΅ν΄ λ±λ‘ν properties μ€ μ°μ μμκ° λ λμ κ²μ μ΄λ€ κ²μΌκΉμ? <br>
> * μ λ΅μ VM optionsκ° λ μ°μ μμκ° λμ΅λλ€! λ λ€ λμΌν μ΅μλͺμΌλ‘ μ§μ  ν μ€ννλ©΄ μ΄λκ²μ΄ μ°μ μμκ° λ λμμ§ νμΈν  μ μμ΅λλ€. 


---
## κΈ°ν μ°Έκ³ μ¬ν­
* μ€νλ§ λΆνΈμ μΈλΆ μ€μ  μ°Έκ³ 
   *  κΈ°λ³Έ νλ‘νΌν° μμ€ μ§μ (application.properties)
   *  νλ‘νμΌκΉμ§ κ³ λ €ν κ³μΈ΅ν νλ‘νΌν° μ°μ  μμ μ κ³΅