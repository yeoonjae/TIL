> 📚 본 글은 토비의 스프링 3.1을 읽고 정리한 글입니다. 

<br>

> 📝 들어가며 <br>
> 스프링은 `IoC/DI`, `서비스 추상화`, `AOP` 의 세 가지 프로그래밍 모델을 지원한다. 
> * `IoC/DI` : 오브젝트의 생명주기와 의존관계에 대한 프로그래밍 모델로 확장성과 유연한 코드를 만들 수 있도록 한다. 
> * `서비스 추상화` : 서버 또는 특정 기술에 종속되지 않은 이식성이 뛰어난 애플리케이션을 만들 수 있도록 유연한 추상계층을 두는 방법이다. 
> * `AOP` : 애플리케이션 코드에서 부가적인 기능을 독립적으로 모듈화 시켜주는 것이다. 이를 이용해서 엔터프라잊 서비스를 적용하고도 깔끔한 코드를 유지할 수 있도록 해준다. 

<br>

# **1장 IoC 컨테이너와 DI (작성중)**
## **🔍 1.1  IoC 컨테이너 : bean Factory 와 Application Context**
* 스프링은 오브젝트의 생성과 관계설정, 사용, 제거 등의 작업을 독립된 컨테이너가 담당한다. 
* 컨테이너가 코드 대신 오브젝트에 대한 제어권을 갖고있다 해서 IoC 라고 부른다.
    * 그래서 스프링을 IoC 컨테이너라고 부른다. 

스프링에선 IoC를 담당하는 컨테이너를 빈 팩토리 또는 Application Context라고 부르기도 한다. **Application Context는 IoC와 DI를 위한 빈 팩토리이면서 그 이상의 기능**을 가졌다. 

bean Factory와 Application Context는 각각 `BeanFactory`와 `ApplicationContext` 라는 인터페이스로 정의되어 있다. 

### **1.1.1 IoC 컨테이너를 이용해 애플리케이션 만들기**

다음과 같이 `ApplicationContext` 구현 클래스의 인스턴스를 만들어 IoC 컨테이너를 준비해보자. 
```java
SataticApplicationContext ac = new StatucApplicationContext();
```
이렇게 만들어진 IoC 컨테이너가 컨테이너로서 동작하려면 **POJO 클래스**와 **설정 메타정보**가 필요하다. 다음과 같은 구조로 POJO 클래스와 설정 메타정보를 만들어보자. 

![image](https://user-images.githubusercontent.com/63777714/149875710-ad8ae77e-3656-4ece-9772-c9b083685dfc.png)


```java
// Hello 클래스
public class Hello {
    String name;
    Printer printer;

    public String sayHello() {
        // 프로퍼티로 DI 받은 이름을 이용해 인사문구 생성
        return "Hello " + name; 
    }

    public void print() {
        this.printer.print(sayHello());
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setPrinter(Printer printer) {
        this.printer = printer;
    }
}
```
```java
public interface Printer {
    void printer(String message);
}
```
```java
public class StringPrinter implements Printer {
    private StringBuffer buffer = new String Buffer();

    public void print(String message) {
        this.buffer.append(message);
    }

    public String toString() {
        return this.buffer.toString();
    }
}
```
```java
// 구현 클래스 
public void ConsolePrinter implements Printer {
    public void print(String message) {
        System.out.println(message);
    }
}
```
각 기능에 충실하고 독립적이면서 결합도가 낮은 POJO 클래스를 만들어줬다. 

### 설정 메타정보
 * IoC 컨테이너는 오브젝트를 생성하고 관리하는 역할을 한다. 
 * 스프링 컨테이너가 관리하는 오브젝트를 bean이라 부른다. 
 * 설정 메타정보는 bean을 어떻게 만들고 어떻게 동작하게 할 것인가에 관한 정보이다. 
 * 스프링의 메타정보는 XML 파일이 아니다. 
    * 스프링이 XML 파일에 담긴내용을 읽어서 설정 메타정보로 활용하는 것이다. 
* **스프링의 설정 메타정보는 `BeanDefinition` 인터페이스로 표현되는 순수한 추상정보이다.**
* <u>Application Context는 이 `BeanDifinition`으로 만들어진 메타정보를 담은 오브젝트를 사용해 IoC 와 DI 작업을 수행</u>한다. 
* 즉, `BeanDefinition` 인터페이스를 구현한 설정은 `BeanDefinitionReader` 인터페이스에 의해 원본의 포맷 자료 특성에 맞게 변환해주어 읽어들인다. 

IoC 컨테이너가 사용하는 빈 메타정보는 다음과 같다. 
1. 빈 아이디, 이름, 별칭 : 빈 오브젝트를 구분할 수 있는 식별자
2. 클래스 또는 클래스 이름 : 빈으로 만들 POJO 클래스 또는 서비스 클래스 정보
3. 스코프 : 싱글톤, 프로토타입과 같은 빈의 생성 방식과 존재 범위
4. 프로퍼티 값 또는 참조 : DI에 사용할 프로퍼티 이름과 값 또는 참조하는 빈의 이름
5. 생성자 파라미터 값 또는 참조 : DI에 사용할 생성자 파라미터 이름과 값 또는 참조할 빈의 이름
6. 지연된 로딩 여부, 우선 빈 여부, 자동 와이어링 여부, 빈 패토리 이름, 부모 빈 설정 등

스프링 IoC 컨테이너는 **각 빈에 대한 정보를 담은 설정 메타정보를 읽어들인 뒤에, 이를 참고해서 빈 오브젝트를 생성하고 프로퍼티나 생성자를 통해 의존 오브젝트를 주입해주는 DI 작업을 수행**한다. 이 작업을 통해 만들어지고 DI로 연결되는 오브젝트들이 모여서 하나의 애플리케이션을 구성하고 동작하게 된다. 

![image](https://user-images.githubusercontent.com/63777714/149879410-b0f4d585-3999-447a-a140-4d6c043c9b3f.png)

BeanDefinition을 이용해서 빈으로 등록할 수도 있다. 
```xml
<!--빈 메타정보를 담은 오브젝트를 만든다. 빈 클래스는 Hello로 지정-->
BeanDefinition helloDef = new RootBeanDefinition(Hello.class);
<!--빈의 name 프로퍼티에 들어갈 값을 지정-->
helloDef.getPropettyValues().addPropertyValue("name","Spring");
<!--앞에서 생성한 메타정보를 hello2라는 이름을 가진 빈으로 해서 등록한다. -->
ac.registerBeanDefinition("hello2","helloDef");
```

* IoC 컨테이너는 일단 빈 오브젝트가 생성되고 관계가 만들어지면 그 뒤로는 거의 관여하지 않는다. 
* 기본적으로 싱글톤 빈은 ApplicationContext의 초기화 작업 중에 모두 만들어진다. 

### **1.1.2 IoC 컨테이너의 종류와 사용 방법**

**StaticApplicationContext**
* 코드를 통해 빈 메타정보를 등록하기 위해 사용
* 실제로는 잘 쓰이지 않음

**GenericApplicationContext**
* 가장 일반적인 Application Context 구현 클래스
* 실전에서 사용될 수 있는 모든 기능으 ㄹ갖춘 ApplicationContext
* 컨테이너의 주요 기능을 DI를 통해 확장할 수 있도록 설계
* StaticApplicationContext와는 달리 XML 파일과 같은 외부의 리소스에 있는 빈 설정 메타정보를 Reader를 통해 읽어들여서 메타정보로 전환해서 사용
* XML 로 작성된 빈 설정정보 리더는 대표적으로 `XmlBeanDefinitionReader` 이다. 
> 🔍 과정 알아보기<br>
> 1. 특정 포맷의 빈 설정 메타정보를 확인한다.
> 2. `BeanDefinitionReader`를 구현한 오브젝트에서 Application Context 가 사용할 수 있는 `BeanDefinition` 정보로 변환한다. 

```java
@Test
public void genericApplicationContext() {
    GenericApplicationContext ac = new GenericApplicationContext();
    
    GenericApplicationContext reader = new XmlBeanDefinitionReader(ac);
    reader.loadBeanDefinitions("외부 리소스 파일 위치 (XML 파일)");
    
    ac.refresh(); // 모든 메타정보가 등록이 완료되었으니 Application 컨테이너를 초기화하라는 명령
    
    Hello hello = ac.getBean("hello", Hello.class);
    hello.print();

    assertThat(ac.getBean("printer").toString(), is("Hello Spring"));
}	
```
위의 코드는 XML 파일인 리소스(설정정보)를 XML 설정정보 리더인 `XmlBeanDefinitionReader`를 통해 `GenericApplicationContext`가 이용하도록 하여 Hello bean과 Printer Bean 을 등록하고 사용하게 만든 코드이다. 

* 외부 리소스 파일 위치에 스트링을 넘기면 기본저긍로 classPath로 인식한다. 
    * classPath: , file:, http: 와같은 접두어도 사용 가능하다.
* **스프링 IoC 컨테이너가 사용할 수 있는 `BeanDefinition` 오브젝트로 변환만 된다면 어떤 포맷으로 만들어진 설정 메타정보라도 상관없다.**

`GenericApplicationContext`을 실제로 직접 만들고 설정하는 일은 드물지만, 사실상 많이 쓰인다. 현재 JUnit 테스트 시 생성되는 Application Context 가 바로 `GenericApplicationContext`이다. 

**GenericXmlApplicationContext**
* `GenericApplicationContext`와 `XmlBeanDefinitionReader`가 결합된 `GenericXmlApplicationContext`가 존재한다. 위에 사용했던 예제처럼 따로 적을 필요가 없다. 

**WebApplicationContext**
* 스프링 애플리케이션에서 가장 많이 사용되는 Application Context이다. 
* Application Context 를 확장한 인터페이스이다. 
    * WebApplicationContext를 사용한다는 것은 이를 구현한 구현체를 사용한다는 것이다. 
* 웹 환경에서 사용할 떄 필요한 기능이 추가된 Application Context 이다. 
* `XmlWebApplicationContext` : XML 설정파일을 사용하도록 만들어진 것, XML 외의 설정정보 리소스도 사용할 수 있다. 
* `AnnotationConfigWebApplicationContext` : 어노테이션을 이용한 설정 리소스만 이용할 경우 사용
* 특징 : 자신이 만들어지고 동작하는 환경인 웹 모듈에 대한 정보에 접근할 수 있다. 

![image](https://user-images.githubusercontent.com/63777714/150086618-d82f6010-04a9-4ae6-b20c-205bdf1e0ef3.png)

위의 그림은 웹 환경에서 스프링 빈으로 이뤄진 애플리케이션이 동작하는 구조다.

1. 서블릿 컨테이너는 브라우저와 같은 클라이언트로부터 들어오는 요청을 받아서 서블릿을 동작시켜주는 일을 맡는다.
2. 서블릿은 웹 애플리케이션이 시작될 때 미리 만들어둔 웹 애플리케이션 컨텍스트에게 빈 오브젝트로 구성된 애플리케이션의 기동 역할을 해줄 빈을 요청해서 받아둔다.
3. 그리고 미리 지정된 메소드를 호출함으로써 스프링 컨테이너가 DI 방식으로 구성해둔 애플리케이션의 기능이 시작되는 것이다.


> 스프링은 웹 환경에서의 Appliction Context를 생성하고 설정 메타정보로 초기화해주고, 클라이언트로부터 들어오는 요청마다 적절한 빈을 찾아서 이를 실행해주는 `DispatcherServlet` 을 제공해준다. <br> 
### **1.1.3 IoC 컨테이너 계층구조**
* 모든 Application Context는 부모 Application Context를 가질 수 있다. 

![image](https://user-images.githubusercontent.com/63777714/150066787-11b849dc-d5c9-4142-88a4-fab4143d3a45.png)

* 자식 컨텍스트는 빈을 검색하기 위해 루트 컨텍스트까지 요청할 수 있지만, 부모 컨텍스트는 자식 컨텍스트에게 요청하지 않는다. (위로만 향하는 구조 - 때문에 형제 컨텍스트도 찾지 못함)
* 빈 검색 순서는 **자신이 먼저 그 다음 부모** 동일한 빈이 발견되면 자신의 것의 빈이 우선순위를 갖는다. 

> 🤷‍♂️ Why? 왜 이렇게 만들었을까?<br>
> 용도와 성격이 달라서 웹 모듈을 여러 개로 분리하긴 했지만 핵심 로직을 담은 코드를 공유하고 싶을 때 이렇게 구성된다.
> > 💡 궁금한 점 (상속과 같은 느낌인건가..?) 핵심로직은 동일하지만, 다른 성격을 갖고있는 애들은 확장 또는 다형성을 이용해 정의할 수 있는 것 처럼...

✔ Application Context의 계층 구조를 사용할 때는 어떤 것이 루트인지 어떤것이 부모인지 정확히 인지하고 사용해야 한다. 

### **1.1.4 웹 애플리케이션의 IoC 컨테이너 구성**
* 몇 개의 서블릿이 중앙집중식으로 모든 요청을 다 받아서 처리하는 방식을 <u>프론트 컨트롤러 패턴</u>이라고 한다. 
    * 스프링은 이 패턴을 사용한다. 

**웹 어플리케이션의 컨텍스트 구성 방법**
1. 서블릿 컨텍스트와 Root Application Context 계층구조
2. Root Application Context 단일구조
3. 서블릿 컨텍스트 단일구조

**Root Application Context 등록**
* 웹 애플리케이션의 시작과 종료 시 발생하는 이벤트를 처리하는 리스너인 `ServletContextListener`를 이용해 Root Web Application Context를 등록한다. 
* 스프링은 `ServletContextListener`와 같은 기능을 가진 `ContextLoaderListener`를 제공한다. 
* `ContextLoaderListener` : 웹 애플리케이션이 시작할 때 자동으로 루트 애플리케이션 컨텍스트를 만들고 초기화 해준다. 
    * default 값
        * Application Context class : XMlWebApplicationContext
        * XML 파일 생성 위치 : /WEB-INF/applicationContext.xml
    * Context Class를 변경하고 싶으면 contextClass 파라미터를 지정하면 된다. 하지만 반드시 WebApplicationContext 인터페이스를 구현한 클래스여야 한다. 

**서블릿 ApplicationContext 등록**
* 스프링의 웹기능을 지원하는 프론트 컨트롤러 서블릿은 **DisparcherServlet**이다. 
* <u>DispatcherServlet은 서블릿이 초기화 될 떄 자신만의 컨텍스트를 생성하고 초기화</u>한다. 
* 동시에 <u>웹 어플리케이션 레벨에 등록된 Root ApplicationContext를 찾아서 이를 자신의 부모 컨텍스트로 사용</u>한다. 
* 여러개의 DispatcherServlet 도 등록이 가능하며 네임스페이스로 구분한다. 

---
## 🔍 **1.2 IoC/DI를 위한 빈 설정 메타정보 작성**

![image](https://user-images.githubusercontent.com/63777714/150466106-9dd29f1f-2d30-4785-ad88-abcb11afe105.png)

* 메타정보를 통해 IoC 컨테이너는 자신이 만들 오브젝트인지 구분한다. 
* 하나의 메타정보로 여러개의 빈 오브젝트를 만들 수 있기 때문에 빈의 이름이나 아이디를 나타내는 정보는 포함되지 않는다. 
* 컨테이너에 빈의 메타정보를 등록할 때 꼭 필요한 것은 **클래스 이름과 빈의 아이디 또는 이름**이다. 
<details>
<summary>빈 설정 메타정보 항목</summary>
<div markdown="1">
|이름|내용|디폴드 값|
|--|--|--|
|beanClassName| 빈 오브젝트의 클래스 이름. 이 클래스의 인스턴스가 된다.|X. 필수항목|
|parentName|빈 메타정보를 상속받을 부모 BeanDefinition의 이름. 빈의 메타정보는 계층구조로 상속 가능|X|
|factoryBeanName|팩토리 역할을 하는 빈을 이용해 빈 오브젝트를 생성하는 경우에 팩토리 빈의 이름을 지정한다.|X|
|factoryMethodName|다른 빈 또는 클래스의 메소드를 통해 빈 오브젝트를 생성하는 경우 그 메소드 이름을 지정한다.|X|
|scope|빈 오브젝트의 생명주기를 결정하는 스코프 지정.|싱글톤|
|lazyInit|빈 오브젝트의 생성을 최대한 지연할지 여부. true일 경우 컨테이너는 빈 오브젝트의 생성을 꼭 필요한 시점까지 미룬다.|false|
|dependsOn|먼저 만들어져야 하는 빈 지정.|X|
|autowireCandidate|명시적인 설정이 없어도 미리 정해진 규칙을 가지고 자동으로 DI 후보를 결정하는 자동 와이어링 대상으로 포함시킬지 여부|true|
|primary|자동 와이어링 작업 중 DI대상 후보가 여러개인 경우 우산권을 부여하는 여부. 빈이 여러개일 경우 이 설정이 없다면 예외 발생|false|
|abstract|메타정보 상속에만 사용할 추상 빈으로 만들지 여부|X|
|dependencyCheck|프로퍼티 값 또는 레퍼런스가 모두 설정되어 있는지를 검증하는 작업의 종류|X|
|initMethod|빈이 생성되고 DI를 마친 뒤 실행할 초기화 메소드의 이름|X|
|destoryMethod|빈의 생명주기가 다 돼서 제거하기 전에 호출할 메소드의 이름|X|
|propertyValue|프로퍼티의 이름과 설정 값 또는 레퍼런스(수정자 DI 작업에서 사용)|X|
|annotationMetadata|빈 클래스에 담긴 애노테이션과 그 attrubute 값. 애노테이션을 이용한 설정에서|X|
</div>
</details>
 <br>

### **빈 등록 방법**
1. **XML : `<bean>` 태그**
```xml
<bean id="hello" class="spring.learningtest.spring.ioc.bean.Hello">
...
</bean>
```
```xml
<bean id="hello" class="spring.learningtest.spring.ioc.bean.Hello">
    <property name="printer">
        <bean ... >
    </property>
</bean>
```
2. **XML : 네임스페이스와 전용 태그**
* 스프링은 DI의 원리를 Application Context자신에게도 적용한다. 
* 하지만, `<bean>` 태그와 구분이 잘 되지 않기 때문에 `<aop:pointcut>` 와 같은 네임스페이스와 태그를 가진 설정 방법을 제공한다. 
* 이 외에도 많은 네임스페이스아 태그를 제공한다. 물론 직접 커스텀 태그를 만들어서 적용할 수도 있다. 
    * 🤷‍♂️ How? 스프링 컨테이너가 빈을 만들 때 사용하는 설정 메타정보가 특정 XML 문서나 태그, 포맷에 종속되지 않는 독립적인 오브젝트이기 때문이다. 

3. **자동인식을 이용한 빈 등록 : 스테레오타입 애노테이션과 빈 스캐너**
* **빈 스캐닝** : 특정 어노테이션을 붙은 클래스를 자동으로 찾아서 빈으로 등록해주는 기능
* **빈 스캐너** : 빈 스캐닝 작업을 담당하는 오브젝트를 말한다. 
    * 지정된 클래스패스 아래에 있는 모든 패키지의 클래스를 대상으로 필터를 적용하여 빈 등록할 클래스들을 선별한다. 
    * 빈 스캐너에 내장된 default Filter는 **@Component 애노테이션과 이를 메타 애노테이션으로 가진 애노테이션이 부여된 클래스**이다. 
> 이 방식을 사용하면 클래스를 검색하니 클래스 이름은 알 것 이고 빈의 이름은 클래스의 앞글자만 소문자로 변경한 것으로 자동인식한다. 또는 `@Component("빈이름")` 으로 직접 지정할 수도 있다. 
* 커스텀하여 애노테이션을 만들수도 있다. 
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface BusinesRule {
    String value() default "";
}
```
