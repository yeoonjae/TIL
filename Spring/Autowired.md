> π λ³Έ κΈμ μΈνλ° κ°μ 'μ€νλ§ ν΅μ¬ κΈ°μ 'μ λ£κ³  μ λ¦¬ν κΈμλλ€. 

<br>

# **@Autowired**

`@Autowired` λ νμν μμ‘΄ κ°μ²΄μ 'νμ'μ ν΄λΉνλ λΉμ μ°Ύμ μ£Όμν©λλ€. 
μ¦, μμ‘΄μ± μ£Όμκ³Ό κ΄λ ¨λ μ΄λΈνμ΄μ μλλ€. `@Autowired`λ₯Ό λΆμλλ° λΉμΌλ‘ λ±λ‘λμ΄μμ§ μμΌλ©΄ wasκ΅¬λ μ μλ¬κ° λ©λλ€. 

---
## **@Autowiredλ₯Ό μ¬μ©ν  μ μλ μμΉ**

* @Autowired μ required λΌλ μ€μ μ΄ μ‘΄μ¬ν©λλ€. μ΄ μ€μ μ κΈ°λ³Έκ°μ `false`μλλ€. 
* μ¬μ©ν  μ μλ μμΉλ λ€μκ³Ό κ°μ΅λλ€. 
  * μμ±μ
  * Setter
  * νλ

μμλ λ€μκ³Ό κ°μ΅λλ€. 
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
    // setter : required μ€μ μ μ¬μ©νμ¬ falseλ‘ μ§μ ν΄μ£ΌμκΈ° λλ¬Έμ μ΄ μ€νμ μ€λ₯κ° λμ§ μμ΅λλ€. 
    @Autowired(required = false)
    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}
```
```java
@Service
public class BookService {

    // νλμμμ μ¬μ©
    @Autowired  
    BookRepository bookRepository;

}

```
---
## **Beanμ΄ μ¬λ¬ κ° μΌ κ²½μ°**
μμ μμ λ `@Autowired`λ₯Ό μ¬μ©νμ¬ μμ‘΄μ± μ£Όμμ νλ λΉμ΄ νλμΌ κ²½μ°μ μμμλλ€. κ·Έλ λ€λ©΄ λ§μ½ BookRepositoryκ° μ¬λ¬κ°λΌλ©΄ μ΄λ¨κΉμ? νΉμ ν΄λΉνλ νμμ΄ μλ€λ©΄ μ΄λ»κ² λ κΉμ? λ€μκ³Ό κ°μ μ½λκ° μλ€κ³  κ°μ ν΄λ΄μλ€. 

```java
// BookRepository μΈν°νμ΄μ€ 
public interface BookRepository {
}

// BookRepository μΈν°νμ΄μ€λ₯Ό κ΅¬νν ν΄λμ€1
@Repository
public class MyBookRepsitory implements BookRepository{

}

// BookRepository μΈν°νμ΄μ€λ₯Ό κ΅¬νν ν΄λμ€2
@Repository
public class YeonjaeBookRepsitory implements BookRepository{

}
```
```java
@Service
public class BookService {

    // Autowiredλ₯Ό μ¬μ©νμ¬ BookRepository μμ‘΄μ±μ μ£Όμλ°μ΅λλ€. 
    @Autowired
    BookRepository bookRepository;
}
```
μμ μ½λλ₯Ό μ€ννλ©΄ μλ¬κ° λ©λλ€. BookRepositoryλ₯Ό κ΅¬νν κ΅¬νμ²΄κ° 2κ°μΈλ° Serviceμμλ μ΄λ€ κ²μ μ£Όμλ°μμΌ ν  μ§ λͺ¨λ₯΄κΈ° λλ¬Έμλλ€. 

### **`@Autowired`λ λ€μκ³Ό κ°μ μμλ‘ λΉμ μ°Ύμμ μ£Όμν©λλ€.**
*  ν΄λΉ νμμ λΉμ΄ μλκ°?
    * μλ€λ©΄ Error
    * μλ€λ©΄ λΉμ΄ ν κ° μΈκ°?
        * νλμΈ λΉμ κ°μ²΄μ ν λΉ
        * μ¬λ¬κ°λΌλ©΄ idκ° μΌμΉνλ λΉμ΄ μλμ§ νμΈ --> μλ€λ©΄ μλ¬ 
             

μλλ κ°μ νμμ λΉμ΄ μ¬λ¬κ°μΌ λ μ¬μ©ν  μ μλ λ°©λ²μλλ€. 

1. `@Primary` μ΄λΈνμ΄μ μ¬μ©νμ¬ μ¬μ©ν  λΉ νμνκΈ°. λ€μκ³Ό κ°μ΄ λ³κ²½νκ³  λ€μ μ€ννλ©΄ @Primaryκ° λΆμ΄μλ λΉμ μ£Όμλ°κ² λ©λλ€. **<u>μΆμ²</u>**
```java
@Repository @Primary
public class YeonjaeBookRepository implements BookRepository{

}
```
2. `@Qualifier`λ₯Ό μ¬μ©νμ¬ νΉμ  ν΄λμ€ μ΄λ¦ μ§μ νκΈ°. λ€μκ³Ό κ°μ΄ μ§μ νλ©΄ YeonjaeBookRepositoryλ₯Ό μ£Όμλ°κ² λ©λλ€. 
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
3. ν΄λΉ νμμ λΉ λͺ¨λ μ£Όμλ°κΈ°. Listννλ‘ BookRepositoryλ₯Ό λͺ¨λ λ°μ΅λλ€. System.out.printlnμ ν΅ν΄ λͺ¨λ μ°μ΄λ³΄λ©΄ λΉμΌλ‘ λ±λ‘ν λͺ¨λ  BookRepositoryκ° λμ€λ κ²μ νμΈν  μ μμ΅λλ€. 
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

## **λμ μλ¦¬**

### **BeanPostProcessorλ?**
`BeanPostProcessor`λ μ΄κΈ°ν λΌμ΄ν μ¬μ΄ν΄ μ΄μ κ³Ό μ΄νμ λΆκ°μ μΈ μμμ ν  μ μλ μΈν°νμ΄μ€λ‘, beanμ΄ λ§λ€μ΄μ§λ μμ  νΉμ μ΄μ , μ΄νμ μΆκ°μ μΌλ‘ μμμ νκ³  μΆμ λ μ¬μ©λ©λλ€. 

  * AutowiredAnnotationBeanPostProcessor
    * BeanPostProcessorμ κ΅¬νμ²΄
    * BeanPostProcessorμ κ΅¬νμ²΄μ΄λ―λ‘ IoC μ»¨νμ΄λμ BeanμΌλ‘ λ±λ‘λμ΄ μμ

> μ¦, `@Autowired`λ `BeanPostProcessor`λΌλ λΌμ΄ν μ¬μ΄ν΄ μΈν°νμ΄μ€μ κ΅¬νμ²΄μΈ `AutowiredAnnotationBeanPostProcessor`μ μν΄ μμ‘΄μ± μ£Όμμ΄ μ΄λ£¨μ΄μ§λλ€. 
* InitializingBean
    * μ΄κΈ°ν λΌμ΄ν μ¬μ΄ν΄
    * Bean μΈμ€ν΄μ€κ° μμ±λλ μμ 

> `@PostConstruct` : λΉ μΈμ€ν΄μ€κ° λ§λ€μ΄μ§ ν λΆκ°μ μΈ μμμ ν  μ μλ μ΄λΈνμ΄μμλλ€. 

---

λλ΅μ μΈ λΌμ΄ν μ¬μ΄ν΄μ λ€μκ³Ό κ°μ΅λλ€. 
`BeanPostProcessor` (before) β `InitializingBean` β `BeanPostProcess` (after)

1. BeanFactoryκ° `BeanPostProcessor` νμμ beanμ μ°Ύμ΅λλ€. 
2. IoC μ»¨νμ΄λμ λ±λ‘λμ΄μλ λ€λ₯Έ μΌλ°μ μΈ Beanμκ² `BeanPostProcessor`λ₯Ό μ μ©ν©λλ€. 
3. λ€λ₯Έ Beanμ `@Autowired` Annotationμ μ²λ¦¬νλ `AutowiredAnnotationBeanPostProcessor`μ λ‘μ§μ΄ μ μ©λ©λλ€. 
4. μμ‘΄μ± μ£Όμμ΄ μΌμ΄λ©λλ€. 



---

references : https://jjingho.tistory.com/6