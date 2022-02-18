# **@SpringBootApplication**

SpringBoot는 main 메소드가 선언된 클래스를 기준으로 실행합니다. 

SpringBoot 로 프로젝트를 생성시 프로젝트 명으로 `@SpringBootApplication` 이 붙은 클래스가 존재하고 그 안에는 main 메소드가 존재합니다. 


```java
@SpringBootApplication
public class SpringTestApplication {

  public static void main(String[] args) {
    SpringApplication.run(SpringTestApplication.class, args);
  }

}
```

`@SpringBootApplication` 어노테이션은 스프링 부트의 가장 기본적인 설정을 선언해줍니다. 

그렇다면 `@SpringBootApplication`이 어떻게 기본적인 설정을 하는지, 어떻게 Spring 내부에서 작동을 하는지에 대해서 알아보겠습니다. 

다음은 `@SpringBootApplication` 의 소스코드 중 일부입니다. 
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

	// ... 
}
```
어노테이션을 만들기 위해선 Target과 Retention을 설정해주는 것은 기본입니다. 그 외의 것을 살펴볼까요?

## `@SpringBootConfiguration`
Spring Boot 응용 프로그램 `@Configuration`을 제공함을 나타냅니다. 구성을 자동으로 찾을 수 있도록 Spring의 표준 `@Configuration` 주석 대신 사용할 수 있습니다. 


##  `@ComponentScan`
 이 어노테이션은 `@component` 어노테이션 및 `@Service`, `@Repository`, `@Controller` 등의 어노테이션을 스캔하여 Spring Bean으로 등록해주는 어노테이션입니다. 

## `@EnableAutoConfiguration`
해당 어노테이션은 사전에 정의한 라이브러리들을 Bean으로 등록하는 어노테이션입니다. 내부소스는 다음과 같습니다. 
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {

	String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

	Class<?>[] exclude() default {};

	String[] excludeName() default {};

}

```
* `@ComponentScan` 을 통해 대상이 되는 객체를 Bean을 등록하고, tomcat 등 스프링에 정의한 외부 의존성을 갖는 class들을 스캔하여 bean 으로 등록합니다. 
* 이 때 정의된 class들은 spring-boot-autoconfigure 안에 들어있는 META-INF 폴더에 `spring.factories` 라는 파일에 정의되어 있습니다. 
* 이 중에서 `org.springframework.boot.autoconfigure.EnableAutoConfiguration`을 key로하는 외부 의존성 class들이 존재하며,
여기에 정의된 모든 class를 가져오는 게 아니라 class에 내부에 정의된 어노테이션에 따라 그 조건에 부합하는 class들만 생성됩니다. 
    * `@ConditionalOnProperty`, `@ConditionalOnClass`, `@ConditionalOnBean` 등

```
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
...
```


## **정리**
따라서 `@SpringBootApplication`이 붙은 클래스가 실행될 때 `@ComponentScan`이 해당되는 어노테이션이 붙은 클래스를 읽어들여 bean으로 등록하고,  `@EnableAutoConfiguration` 에 의해서 환경에 필요한 자동 설정을 bean으로 등록해줍니다.


