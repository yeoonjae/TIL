> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **1. @ComponentScan**


Spring 3.1 부터 도입이 된 것으로 해당 어노테이션을 등록하면 XML 파일에 `<context:component-scan>` 을 사용한 것처럼 빈으로 등록하는 어노테이션이 붙은 빈을 자동으로 스캔하여 등록해줍니다. 

* `@ComponentScan` 의 역할
   * 스캔 위치 설정
   * 필터 : 어떤 어노테이션을 스캔할지 또는 스캔하지 않을지 설정

가장 중요한 설정은 `basePackages` , `basePackageClasses` 설정과 `@Filter`를 통해 스캔을 제외할 대상 설정입니다. 우선  `basePackages` , `basePackageClasses` 는 스캔할 위치를 설정하는 방법입니다. 

`basePackages` 값은 `String` 이기 때문에 패키지가 긴 경우에는 일일이 입력하기에도 번거롭기도 하고 String으로 입력하다가 오타가 날 수도 있기에 TypeSafe 하지 않습니다.

 그래서 TypeSafe 한 방법으로 설정할 수 있는 `basePackageClasses` 를 사용하는 것을 추천합니다. `basePackageClasses` 는 스캔 위치를 클래스의 위치로 받아 스캔을 시작하는 방법입니다. 

 ## **1.1 BasePackageClasses**
`@ComponentScan` 이 붙은 `@Configuration`이 붙은 대상부터 스캔을 시작합니다. 

SpringBoot로 예를 들어보면, `@SpringBootApplication` 어노테이션에 들어가보면 다음과 같이 정의되어 있는 것을 확인할 수 있습니다. 

```java

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {
        @Filter( type = FilterType.CUSTOM,classes = {TypeExcludeFilter.class}), @Filter( type = FilterType.CUSTOM,classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    @AliasFor(annotation = EnableAutoConfiguration.class)
    // 이하 생략...
}
```
`@ComponentScan`과, `@Configuration` 을 구현한 `@SpringBootConfiguration`이 붙어있는 것을 볼 수 있습니다. 

그렇기 때문에 Application이 시작되면 `@SpringBootApplication`이 붙은 클래스를 기점으로 해당 클래스를 포함하는 패키지와 그 이하의 클래스들, 또는 패키지들이 모두 스캔 대상이 됩니다. 

만약,  `@SpringBootApplication`이 붙은 클래스 밖의 패키지가 존재한다면 그 패키지는 스캔 대상이 되지 않습니다. 이해를 위해 사진으로 설명드리겠습니다. 아래와 같은 패키지 구조가 있습니다. 

![image](https://user-images.githubusercontent.com/63777714/139530194-afc73ea1-0840-4c5e-9ad1-676e60255078.png)

빨간색으로 표시된 클래스가 `@SpringBootApplication`이 붙은 클래스입니다. 그럼 dempspring51 패키지에 있는 클래스들은 스캔 대상이 되지만, out 패키지에 있는 클래스는 스캔 대상이 되지 않는다는 것 입니다. 


## **1.2 Filter**
Filter는 어떤 어노테이션을 스캔할지 또는 하지 않을지를 설정할 수 있습니다. 만약 스캔에서 제외할 대상이 있다면 `excludeFilters` 엘리먼트에 @Filter 어노테이션을 사용하여 지정하면 됩니다. 1.1 BasePackageClasses를 설명하면서 보여드렸던 `@SpringBootApplication`에 보면 `excludeFilters`에 @Filter 를 사용하여 어떤 클래스를 제외할 지 설정한 것을 볼 수 있습니다. 


---
# **2. @Component**

`@Component` 가 붙은 클래스는 `@ComponentScan` 을 통해 자동으로 빈으로 등록됩니다. 정확하게는 `@Component`가 붙은 클래스 또는 `@Component`를 메타 어노테이션으로 갖고 있는 어노테이션을 어노테이션이 붙은 클래스가 자동 빈 등록 대상이 됩니다. 

`@Component`를 메타 어노테이션으로 갖고 있는 어노테이션은 다음과 같습니다. 
* @Controller
* @Repository
* @Service
* @Configuration

---
# **3. @ComponentScan의 동작원리**

실제 스캐닝은 BeanFactoryPostProcessor을 구현한 ConfigurationClassPostProcessor에
의해 처리 됩니다. 

`BeanFactoryPostProcessor`는 `BeanPostProcessor`와 비슷하지만 실행되는 시점이 다릅니다. `BeanFactoryPostProcessor`는 다른 모든 빈들을 만들기 이전에 적용이 되는 것입니다. 

즉,`@ComponentSacn`은 다른 빈들을 찾아 `BeanFactoryPostProcessor`를 구현한 `ConfigurationClassPostProcessor`에 의해 <u>빈으로 등록</u>이 되며, `@Autowired`는  등록된 다른 빈들을 찾아 `BeanPostProcessor`을 구현한  `AutowiredAnnotationBeanPostProcessor`에 의해 <u>의존성 주입을 적용</u>하는 것 입니다. 

다른 빈들은 @Controller, @Service, @Bean, @Respository 등을 말합니다. 

> 🔍 짚고 넘어가기 <br>
> * 그렇다면 BeanFactoryPostProcessor -> BeanPostProcessor 순으로 동작하는 것이라고 이해해도 될까? 
>     * Yes
>
> 정리하자면 다음과 같다. 
> * BeanPostProcessor 
>    * Spring Bean의 생성 전후에 Bean에 대한 초기화 작업을 수행할 수 있다.
>    * BeanPostProcessor는 빈(또는 객체) 인스턴스상에서 동작한다. 즉, 스프링 IoC 컨테이너는 빈 인스턴스를 인스턴스화한 다음에 BeanPostProcessor가 자신의 일을 수행한다.
> * BeanFactoryPostProcessor
>    * Bean의 정의 자체를 바꿀 수 있다. BeanPostProcessor보다 먼저 실행된다.
