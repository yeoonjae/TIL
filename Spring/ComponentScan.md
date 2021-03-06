> π λ³Έ κΈμ μΈνλ° κ°μ 'μ€νλ§ ν΅μ¬ κΈ°μ 'μ λ£κ³  μ λ¦¬ν κΈμλλ€. 

<br>

# **1. @ComponentScan**


Spring 3.1 λΆν° λμμ΄ λ κ²μΌλ‘ ν΄λΉ μ΄λΈνμ΄μμ λ±λ‘νλ©΄ XML νμΌμ `<context:component-scan>` μ μ¬μ©ν κ²μ²λΌ λΉμΌλ‘ λ±λ‘νλ μ΄λΈνμ΄μμ΄ λΆμ λΉμ μλμΌλ‘ μ€μΊνμ¬ λ±λ‘ν΄μ€λλ€. 

* `@ComponentScan` μ μ­ν 
   * μ€μΊ μμΉ μ€μ 
   * νν° : μ΄λ€ μ΄λΈνμ΄μμ μ€μΊν μ§ λλ μ€μΊνμ§ μμμ§ μ€μ 

κ°μ₯ μ€μν μ€μ μ `basePackages` , `basePackageClasses` μ€μ κ³Ό `@Filter`λ₯Ό ν΅ν΄ μ€μΊμ μ μΈν  λμ μ€μ μλλ€. μ°μ   `basePackages` , `basePackageClasses` λ μ€μΊν  μμΉλ₯Ό μ€μ νλ λ°©λ²μλλ€. 

`basePackages` κ°μ `String` μ΄κΈ° λλ¬Έμ ν¨ν€μ§κ° κΈ΄ κ²½μ°μλ μΌμΌμ΄ μλ ₯νκΈ°μλ λ²κ±°λ‘­κΈ°λ νκ³  StringμΌλ‘ μλ ₯νλ€κ° μ€νκ° λ  μλ μκΈ°μ TypeSafe νμ§ μμ΅λλ€.

 κ·Έλμ TypeSafe ν λ°©λ²μΌλ‘ μ€μ ν  μ μλ `basePackageClasses` λ₯Ό μ¬μ©νλ κ²μ μΆμ²ν©λλ€. `basePackageClasses` λ μ€μΊ μμΉλ₯Ό ν΄λμ€μ μμΉλ‘ λ°μ μ€μΊμ μμνλ λ°©λ²μλλ€. 

 ## **1.1 BasePackageClasses**
`@ComponentScan` μ΄ λΆμ `@Configuration`μ΄ λΆμ λμλΆν° μ€μΊμ μμν©λλ€. 

SpringBootλ‘ μλ₯Ό λ€μ΄λ³΄λ©΄, `@SpringBootApplication` μ΄λΈνμ΄μμ λ€μ΄κ°λ³΄λ©΄ λ€μκ³Ό κ°μ΄ μ μλμ΄ μλ κ²μ νμΈν  μ μμ΅λλ€. 

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
    // μ΄ν μλ΅...
}
```
`@ComponentScan`κ³Ό, `@Configuration` μ κ΅¬νν `@SpringBootConfiguration`μ΄ λΆμ΄μλ κ²μ λ³Ό μ μμ΅λλ€. 

κ·Έλ κΈ° λλ¬Έμ Applicationμ΄ μμλλ©΄ `@SpringBootApplication`μ΄ λΆμ ν΄λμ€λ₯Ό κΈ°μ μΌλ‘ ν΄λΉ ν΄λμ€λ₯Ό ν¬ν¨νλ ν¨ν€μ§μ κ·Έ μ΄νμ ν΄λμ€λ€, λλ ν¨ν€μ§λ€μ΄ λͺ¨λ μ€μΊ λμμ΄ λ©λλ€. 

λ§μ½,  `@SpringBootApplication`μ΄ λΆμ ν΄λμ€ λ°μ ν¨ν€μ§κ° μ‘΄μ¬νλ€λ©΄ κ·Έ ν¨ν€μ§λ μ€μΊ λμμ΄ λμ§ μμ΅λλ€. μ΄ν΄λ₯Ό μν΄ μ¬μ§μΌλ‘ μ€λͺλλ¦¬κ² μ΅λλ€. μλμ κ°μ ν¨ν€μ§ κ΅¬μ‘°κ° μμ΅λλ€. 

![image](https://user-images.githubusercontent.com/63777714/139530194-afc73ea1-0840-4c5e-9ad1-676e60255078.png)

λΉ¨κ°μμΌλ‘ νμλ ν΄λμ€κ° `@SpringBootApplication`μ΄ λΆμ ν΄λμ€μλλ€. κ·ΈλΌ dempspring51 ν¨ν€μ§μ μλ ν΄λμ€λ€μ μ€μΊ λμμ΄ λμ§λ§, out ν¨ν€μ§μ μλ ν΄λμ€λ μ€μΊ λμμ΄ λμ§ μλλ€λ κ² μλλ€. 


## **1.2 Filter**
Filterλ μ΄λ€ μ΄λΈνμ΄μμ μ€μΊν μ§ λλ νμ§ μμμ§λ₯Ό μ€μ ν  μ μμ΅λλ€. λ§μ½ μ€μΊμμ μ μΈν  λμμ΄ μλ€λ©΄ `excludeFilters` μλ¦¬λ¨ΌνΈμ @Filter μ΄λΈνμ΄μμ μ¬μ©νμ¬ μ§μ νλ©΄ λ©λλ€. 1.1 BasePackageClassesλ₯Ό μ€λͺνλ©΄μ λ³΄μ¬λλ Έλ `@SpringBootApplication`μ λ³΄λ©΄ `excludeFilters`μ @Filter λ₯Ό μ¬μ©νμ¬ μ΄λ€ ν΄λμ€λ₯Ό μ μΈν  μ§ μ€μ ν κ²μ λ³Ό μ μμ΅λλ€. 


---
# **2. @Component**

`@Component` κ° λΆμ ν΄λμ€λ `@ComponentScan` μ ν΅ν΄ μλμΌλ‘ λΉμΌλ‘ λ±λ‘λ©λλ€. μ ννκ²λ `@Component`κ° λΆμ ν΄λμ€ λλ `@Component`λ₯Ό λ©ν μ΄λΈνμ΄μμΌλ‘ κ°κ³  μλ μ΄λΈνμ΄μμ μ΄λΈνμ΄μμ΄ λΆμ ν΄λμ€κ° μλ λΉ λ±λ‘ λμμ΄ λ©λλ€. 

`@Component`λ₯Ό λ©ν μ΄λΈνμ΄μμΌλ‘ κ°κ³  μλ μ΄λΈνμ΄μμ λ€μκ³Ό κ°μ΅λλ€. 
* @Controller
* @Repository
* @Service
* @Configuration

---
# **3. @ComponentScanμ λμμλ¦¬**

μ€μ  μ€μΊλμ BeanFactoryPostProcessorμ κ΅¬νν ConfigurationClassPostProcessorμ
μν΄ μ²λ¦¬ λ©λλ€. 

`BeanFactoryPostProcessor`λ `BeanPostProcessor`μ λΉμ·νμ§λ§ μ€νλλ μμ μ΄ λ€λ¦λλ€. `BeanFactoryPostProcessor`λ λ€λ₯Έ λͺ¨λ  λΉλ€μ λ§λ€κΈ° μ΄μ μ μ μ©μ΄ λλ κ²μλλ€. 

μ¦,`@ComponentSacn`μ λ€λ₯Έ λΉλ€μ μ°Ύμ `BeanFactoryPostProcessor`λ₯Ό κ΅¬νν `ConfigurationClassPostProcessor`μ μν΄ <u>λΉμΌλ‘ λ±λ‘</u>μ΄ λλ©°, `@Autowired`λ  λ±λ‘λ λ€λ₯Έ λΉλ€μ μ°Ύμ `BeanPostProcessor`μ κ΅¬νν  `AutowiredAnnotationBeanPostProcessor`μ μν΄ <u>μμ‘΄μ± μ£Όμμ μ μ©</u>νλ κ² μλλ€. 

λ€λ₯Έ λΉλ€μ @Controller, @Service, @Bean, @Respository λ±μ λ§ν©λλ€. 

> π μ§κ³  λμ΄κ°κΈ° <br>
> * κ·Έλ λ€λ©΄ BeanFactoryPostProcessor -> BeanPostProcessor μμΌλ‘ λμνλ κ²μ΄λΌκ³  μ΄ν΄ν΄λ λ κΉ? 
>     * Yes
>
> μ λ¦¬νμλ©΄ λ€μκ³Ό κ°λ€. 
> * BeanPostProcessor 
>    * Spring Beanμ μμ± μ νμ Beanμ λν μ΄κΈ°ν μμμ μνν  μ μλ€.
>    * BeanPostProcessorλ λΉ(λλ κ°μ²΄) μΈμ€ν΄μ€μμμ λμνλ€. μ¦, μ€νλ§ IoC μ»¨νμ΄λλ λΉ μΈμ€ν΄μ€λ₯Ό μΈμ€ν΄μ€νν λ€μμ BeanPostProcessorκ° μμ μ μΌμ μννλ€.
> * BeanFactoryPostProcessor
>    * Beanμ μ μ μμ²΄λ₯Ό λ°κΏ μ μλ€. BeanPostProcessorλ³΄λ€ λ¨Όμ  μ€νλλ€.
