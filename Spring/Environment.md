> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **Environment**


`ApplicationContext` 클래스는 `EnvironmentCapable`을 상속받고 있습니다. 이 `EnvironmentCapable`이 제공하는 기능들을 살펴보도록 하겠습니다. 

---

## **1. 프로파일(Profile)**
 * 빈들의 묶음
 * Environment의 역할을 활성화할 프로파일 확인 및 설정
 * 어떤 특정 환경에서만 빈들이 등록되어야 하는 경우를 충족시키기 위해 등장
 
---
### 1.1 프로파일 유즈케이스
*  테스트 환경에서는 A라는 빈을 사용하고, 배포 환경에서는 B라는 빈을 쓰고 싶다.
*  이 빈은 모니터링 용도니까 테스트할 때는 필요가 없고 배포할 때만 등록이 되면
좋겠다.

위의 경우에 해당할 때 사용합니다. 

---
### 1.2 프로파일 정의하기
1. 클래스에 정의
   * @Configuration @Profile(“test”)
   * @Component @Profile(“test”)

(위의 예시 : test 환경에서만 적용이 되어야 될 때 )

```java
@Configuration
@Profile("test")
public class TestConfiguration {

    @Bean
    public BookRepository bookRepository(){
        return new TestBookRepository();
    };
}

// 또는 ComponentScan으로 등록되는 빈에도 프로파일을 지정할 수 있습니다. 

@Repository
@Profile("test")
public class TestBookRepository implements BookRepository {

}
```

2. 메소드에 정의
   * @Bean @Profile(“test”)

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

### 1.3 프로파일 설정하기
* -Dspring.profiles.avtive=”test,A,B,...”
*  @ActiveProfiles (테스트용)

![image](https://user-images.githubusercontent.com/63777714/139589848-adbe701b-0084-420e-9a78-cac6237a0100.png)

 * 프로파일 설정하는 방법은 우측 상단에 빨간 박스를 클릭하시면 edit Configuration 이라는 메뉴가 있습니다. 그 메뉴를 클릭하시면 그림처럼 보이는 창이 뜨는데, 빨간색 박스로 표시해놓은 Active profile 에 'test' 를 입력하게 되면 test의 프로파일이 적용이 됩니다. 

 * 또는 Active profile 이라는 옵션이 없는 경우에는 Environment 안에 있는 VM options에 `Dspring.profiles.avtive=”test"` 라는 설정을 주어 test 프로파일로 적용시키게도 가능합니다. 

```java
// 실행코드
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
실행결과

      [test]
      [default]


---
### 1.4 프로파일 표현식
 * ! (not)
 * & (and)
 * | (or)

       ex. `@Profile(" ! test")` : test 가 아닐때라는 뜻

 ---
 ## **2. 프로퍼티(Property)**
 어플리케이션에 등록되어 있는 Key-value 쌍으로 제공되는 property 에 접근할 수 있는 기능입니다. 
  * 다양한 방법으로 정의할 수 있는 설정값
  * Environment 의 역할은 프로퍼티 소스 설정 및 프로퍼티 값 가져오기
  * 프로퍼티는 우선 순위가 있습니다. 
     *  ServletConfig 매개변수
     *  ServletContext 매개변수
     *  JNDI (java:comp/env/)
     *  JVM 시스템 프로퍼티 (-Dkey=”value”)
     * JVM 시스템 환경 변수 (운영 체제 환경 변수)

### 2.1 프로퍼티 설정하기 
1. VM-options를 이용한 설정

![image](https://user-images.githubusercontent.com/63777714/139590746-99524fc9-5d6c-4ca0-b893-08acac6d64fa.png)

위의 그림과 같이 VM options에 `-Dapp.name=spring5` 처럼 설정을 할 수 있습니다. 

실행코드 
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
        // environment의 getProperty로 app의 name 꺼내오기 
        System.out.println(environment.getProperty("app.name"));
    }
}

//  실행결과
spring5
```

<br>

2. properties 파일 추가 후 `@PropertySource` 를 통한 설정

![image](https://user-images.githubusercontent.com/63777714/139591254-f0c0f3f2-86a8-429c-b212-c0e40fde7c78.png)

resouces 에 app.properties 파일을 하나 생성 후 

    app.about=spring

다음과 같이 작성해주었습니다. 

그리고 @Configuration이 들어있는 클래스에서 `@PropertySource("classpath:/app.properties")` 다음과 같이 적어주면 properties 등록 및 설정이 적용되는 것을 확인할 수 있습니다. 

```java
// configuration 이 들어있는 클래스 
@SpringBootApplication
@PropertySource("classpath:/app.properties")
public class Dempspring51Application {

    public static void main(String[] args) {
        SpringApplication.run(Dempspring51Application.class, args);
    }
}

// 실행코드
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

// 실행결과
spring5
spring
```

> 🍳그렇다면 VM options 과 PropertySource를 통해 등록한 properties 중 우선순위가 더 높을 것은 어떤 것일까요? <br>
> * 정답은 VM options가 더 우선순위가 높습니다! 둘 다 동일한 옵션명으로 지정 후 실행하면 어느것이 우선순위가 더 높은지 확인할 수 있습니다. 


---
## 기타 참고사항
* 스프링 부트의 외부 설정 참고
   *  기본 프로퍼티 소스 지원 (application.properties)
   *  프로파일까지 고려한 계층형 프로퍼티 우선 순위 제공