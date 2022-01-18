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
