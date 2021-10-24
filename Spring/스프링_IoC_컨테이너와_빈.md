> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>

# 스프링 IoC 컨테이너와 빈 

## Inversion of Control 
 의존 관계 주입(Dependency Injection)이라고도 하며, 어떤 객체가 사용하는 <u>**의존 객체를 직접 만들어 사용하는게 아니라** </u>, <u>**주입 받아 사용하는 방법**</u>을 말 합니다. 

 ## 스프링 IoC 컨테이너
 * BeanFactory (스프링 IoC 컨테이너의 가장 최상위 계층인 인터페이스)
 * 애플리케이션 컴포넌트의 중앙 저장소
 * 빈(Bean) 설정 소스로부터 빈 정의를 읽어들이고, 빈을 구성하고 제공한다. 

 ## 빈(Bean) 
  * 스프링 IoC 컨테이너가 관리하는 객체.

## 빈(Bean)을 구분해보자. 

<br>

1. 아래의 소스는 Bean이 아니다. 

```java
public class Book {

    private Date created;

    private BookStatus bookSataus;

    //..getter/setter 생략
}
```
2. 아래의 소스는 `@Repository` 어노테이션을 붙임으로써 `AutoScan` 에 의하여 `빈(Bean)` 으로 등록이 됩니다. 
```java
@Repository
public class BookRepository {

    public Book save(Book book) {
        return null;
    }

}
```
3. 아래의 소스는 `@Service` 어노테이션을 붙임으로써 역시 `AutoScan` 에 의하여 `빈(Bean)` 으로 등록이 됩니다. 
```java
/**
 * BookService -> BookRepository
 */
@Service
public class BookService {

    @Autowired
    private BookRepository bookSRepository;

    // 의존성 주입을 받을 수 있도록 생성자를 만들었다. 
    public BookService(BookRepository bookSRepository) {
        this.bookSRepository = bookSRepository;
    }

    public Book save(Book book) {
        book.setCreated(new Date());
//        book.setBookStatus(BookStatus.DRAFT);
        return bookSRepository.save(book);
    }
```

위의 예제들은 왜 Bean으로 등록을 하고 관리를 하는 것일까요? (장점)
* Bean으로 등록이 되어야 `@Autowired`를 통해 주입을 받을 수 있습니다. (의존성 관리)
* Bean을 싱글톤 Scope으로 사용하기 위해서 입니다. Spring은 Bean을 등록할 때 아무런 설정을 하지 않으면 기본적으로 빈은 싱글톤 scope를 갖습니다. 
* 라이프사이클 인터페이스를 지원이 됩니다. 

```java
@Service
public class BookService {

    @Autowired
    private BookRepository bookSRepository;
    //생략

    /**
     * @PostConstruct
     * - 의존성 주입이 이루어진 후 초기화를 수행하는 메소드입니다.
     * - 객체의 초기화 부분
     * - 객체가 생성된 후 별도의 초기화 작업을 위해 실행하는 메소드를 선언합니다.
     * - @PostConstruct 어노테이션을 설정해놓은 init 메소드는 WAS가 띄워질 때 실행됩니다.
     * */
    @PostConstruct
    public void postConstuct(){
        System.out.println("===============");
        System.out.println("Hello");
    }
}

```

다음과 같이 코드를 작성하고 실행을 하면 console에 `=============== Hello`이 출력됩니다. 이처럼 `@PostConstruct`어노테이션을 통해 빈으로 등록을 하면 객체가 초기화될 때의 라이프사이클을 관리할 수 있습니다. 



> 🔍 **짚고 넘어가기**<br><br>
> **◽ Scope란** <br>
> 빈이 존재할 수 있는 범위를 뜻합니다. <br><br>
>  🕵🏻‍♂️**싱글톤 Scope이란?**<br>
> 어플리케이션 전반에 걸쳐 해당 빈의 인스턴스를 오직 하나만 생성해서 사용하는 것입니다.<br><br>
> 🕵🏻‍♂️**프로토타입이란?**<br>
> 매번 다른 객체를 말합니다. 프로토타입 빈의 생성과 의존관계 주입까지만 스프링 컨테이너가 관여하고 그 이후로는 관여하지 않는 매우 짧은 범위의 스코프입니다. <br><br>
> ❓ 기본적으로 빈으로 등록할 때 아무런 어노테이션을 붙이지 않았다면 스프링은 기본적으로 싱글톤 scope으로 등록이 됩니다. 즉, 위의 소스를 예로 들어 보자. 어플리케이션 전반에서 스프링 IoC 컨테이너로 부터 BookService를 받아서 사용한다면 그 인스턴스는 항상 같은 객체입니다. <br>이렇게 싱글톤 scope를 사용하면 **메모리 상 효율적**이고, **하나의 객체를 사용하기 때문에 런타임 시 성능 최적화에도 효율적**입니다. 



