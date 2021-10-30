> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# **@Autowired**

`@Autowired` 는 필요한 의존 객체의 '타입'에 해당하는 빈을 찾아 주입합니다. 
즉, 의존성 주입과 관련된 어노테이션 입니다. `@Autowired`를 붙였는데 빈으로 등록되어있지 않으면 was구동 시 에러가 납니다. 

---
## **@Autowired를 사용할 수 있는 위치**

* @Autowired 의 required 라는 설정이 존재합니다. 이 설정의 기본값은 `false`입니다. 
* 사용할 수 있는 위치는 다음과 같습니다. 
  * 생성자
  * Setter
  * 필드

예시는 다음과 같습니다. 
```java
@Service
public class BookService {

    BookRepository bookRepository;

    @Autowired
    public BookService(BookRepository bookrepository) {
        this.bookRepository = bookrepository;
    }

    public void printRepositoryClass(){
        System.out.println(bookRepository.getClass());
    }
}

```
```java
@Service
public class BookService {

    BookRepository bookRepository;
    // setter : required 설정을 사용하여 false로 지정해주었기 떄문에 이 실행은 오류가 나지 않습니다. 
    @Autowired(required = false)
    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}
```
```java
@Service
public class BookService {

    // 필드에서의 사용
    @Autowired  
    BookRepository bookRepository;

}

```
---
## **Bean이 여러 개 일 경우**
위의 예제는 `@Autowired`를 사용하여 의존성 주입을 하는 빈이 하나일 경우의 예시입니다. 그렇다면 만약 BookRepository가 여러개라면 어떨까요? 혹은 해당하는 타입이 없다면 어떻게 될까요? 다음과 같은 코드가 있다고 가정해봅시다. 

```java
// BookRepository 인터페이스 
public interface BookRepository {
}

// BookRepository 인터페이스를 구현한 클래스1
@Repository
public class MyBookRepsitory implements BookRepository{

}

// BookRepository 인터페이스를 구현한 클래스2
@Repository
public class YeonjaeBookRepsitory implements BookRepository{

}
```
```java
@Service
public class BookService {

    // Autowired를 사용하여 BookRepository 의존성을 주입받습니다. 
    @Autowired
    BookRepository bookRepository;
}
```
위의 코드를 실행하면 에러가 납니다. BookRepository를 구현한 구현체가 2개인데 Service에서는 어떤 것을 주입받아야 할 지 모르기 떄문입니다. 

### **`@Autowired`는 다음과 같은 순서로 빈을 찾아서 주입합니다.**
*  해당 타입의 빈이 있는가?
    * 없다면 Error
    * 있다면 빈이 한 개 인가?
        * 하나인 빈을 객체에 할당
        * 여러개라면 id가 일치하는 빈이 있는지 확인 --> 없다면 에러 
             

아래는 같은 타입의 빈이 여러개일 때 사용할 수 있는 방법입니다. 

1. `@Primary` 어노테이션 사용하여 사용할 빈 표시하기. 다음과 같이 변경하고 다시 실행하면 @Primary가 붙어있는 빈을 주입받게 됩니다. **<u>추천</u>**
```java
@Repository @Primary
public class YeonjaeBookRepository implements BookRepository{

}
```
2. `@Qualifier`를 사용하여 특정 클래스 이름 지정하기. 다음과 같이 지정하면 YeonjaeBookRepository를 주입받게 됩니다. 
```java
@Service
public class BookService {

    @Autowired @Qualifier("YeonjaeBookRepository")
    BookRepository bookRepository;

    public void printBookRepository(){
        System.out.println(bookRepository.getClass());
    }
}
```
3. 해당 타입의 빈 모두 주입받기. List형태로 BookRepository를 모두 받습니다. System.out.println을 통해 모두 찍어보면 빈으로 등록한 모든 BookRepository가 나오는 것을 확인할 수 있습니다. 
```java
@Service
public class BookService {

    @Autowired
    List<BookRepository> bookRepositories;

    public void printBookRepository(){
        this.bookRepositories.forEach(s -> System.out.println(s.getClass()));
    }
}
```
---

## **동작 원리**

### **BeanPostProcessor란?**
`BeanPostProcessor`는 초기화 라이프 사이클 이전과 이후에 부가적인 작업을 할 수 있는 인터페이스로, bean이 만들어지는 시점 혹은 이전, 이후에 추가적으로 작업을 하고 싶을 때 사용됩니다. 

  * AutowiredAnnotationBeanPostProcessor
    * BeanPostProcessor의 구현체
    * BeanPostProcessor의 구현체이므로 IoC 컨테이너에 Bean으로 등록되어 있음

> 즉, `@Autowired`는 `BeanPostProcessor`라는 라이프 사이클 인터페이스의 구현체인 `AutowiredAnnotationBeanPostProcessor`에 의해 의존성 주입이 이루어집니다. 
* InitializingBean
    * 초기화 라이프 사이클
    * Bean 인스턴스가 생성되는 시점

> `@PostConstruct` : 빈 인스턴스가 만들어진 후 부가적인 작업을 할 수 있는 어노테이션입니다. 

---

대략적인 라이프 사이클은 다음과 같습니다. 
`BeanPostProcessor` (before) → `InitializingBean` → `BeanPostProcess` (after)

1. BeanFactory가 `BeanPostProcessor` 타입의 bean을 찾습니다. 
2. IoC 컨테이너에 등록되어있는 다른 일반적인 Bean에게 `BeanPostProcessor`를 적용합니다. 
3. 다른 Bean에 `@Autowired` Annotation을 처리하는 `AutowiredAnnotationBeanPostProcessor`의 로직이 적용됩니다. 
4. 의존성 주입이 일어납니다. 



---

references : https://jjingho.tistory.com/6