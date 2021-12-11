> 📚 본 글은 토비의 스프링 3.1을 읽고 정리한 글입니다. 

<br>

# **2장 테스트**
* 스프링이 개발자에게 제공하는 가장 중요한 가치는 객체지향과 테스트이다. 
* 스프링의 핵심인 IoC와 DI는 오브젝트의 설계, 관계, 생성, 사용에 관한 기술로 스프링은 IoC와 DI를 통해 객체지향 프로그래밍 언어의 근본과 가치를 개발자가 손쉽게 적용하고 사용할 수 있도록 도와주는 기술이다. 

---
## **1. UserDaoTest 다시보기**

🤷‍♂️ **테스트**란? **내가 예상하고 의도했던대로, 코드가 정확히 동작하는지를 확인해서 코드에 대한 확신을 하게 해주는 작업**이다. 

### **웹을 통한 DAO 테스트의 문제점**
- Controller, Service, View 까지 만들어 테스트를 해야한다는 불편함이 존재한다. 
- 위에 작성한 이유로 문제가 발생했을 때 문제가 생긴 지점에 대한 파악이 필요하다. 

### **작은 단위의 테스트**
- 웹을 통한 테스트는 위에 작성한 문제점이 존재한다. 따라서, 테스트하고자 하는 대상을 명확화하고 그 대상에만 집중해서 테스트하는 것이 바람직하다.
- 테스트는 가능하면 작은 단위로 쪼개서 집중을 해야한다. 
- **테스트의 관심이 다르다면 테스트할 대상을 분리하고 집중해서 접근해야 한다.** --> 이는 관심사의 분리가 적용된 것이다. 
- 이렇게 작은 단위의 코드에 대해 테스트를 수행한 것을 <u>**단위테스트(unit Test)**</u>라고 한다.
- 단위테스트가 필요한 이유는 다음과 같다. 
    - 개발자가 설계하고 만든 코드가 의도한대로 동작하는지 스스로 빨리 확인받기 위해
        * 이 떄 확인의 대상과 조건이 간단하고 명확할수록 좋다. 

### **자동 수행 테스트 코드**
테스트 자체가 사람의 수작업을 거치는 방법을 사용하기보다는 코드로 만들어져서 자동으로 수행될 수 있게 해야한다. 여기서 말하는 사람의 수작업을 거치는 방법은 테스트를 위해 등록값을 매번 직접 입력하는 경우를 말한다. 
- **장점**
    - 번거로운 작업이 없고, 테스트를 빠르게 실행이 가능하다. 
    - 위의 장점으로 인해 테스트를 자주 반복할 수 있다. 

### **지속적인 개선과 점진적이 개발을 위한 테스트**
* DAO코드를 만들고 DAO로서의 기능에 문제가 없는지 검증해주는 테스트 코드를 만듦으로써 자신감을 가지고 코드를 개선해나가는 작업을 진행할 수 있다. 
* 중간에 잘못 설계했거나, 중요한 코드를 한 줄 빼먹었거나, 수정에 실수가 있었다면 테스트를 통해 무엇이 잘못됐는지 바로 확인할 수 있다. 

또한, UserDao의 기능을 추가하려고 할 때에도 미리 만들어둔 테스트 코드는 유용하게 쓰일 수 있다. 이미 만들어져 있는 코드에 기능을 추가하고, 이를 테스트로 검증하고 테스트를 추가하는 식으로 점진적인 개발이 가능해진다. 테스트를 이용하면 새로운 기능도 기대한 대로 동작하는지 확인할 수 있을 뿐만 아니라, 기존에 만들어뒀던 기능들이 새로운 기능을 추가하느라 수정한 코드에 영향을 받지않고 잘 작동하는지도 확인할 수 있다. 

### **UserDaoTest 의 문제점**
```java
public class UserDaoTest {
	public static void main(String[] args) throws ClassNotFoundException, SQLException {
		ApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
		UserDao dao = context.getBean("userDao", UserDao.class);
		
		User user = new User();
		user.setId("whiteship");
		user.setName("백기선");
		user.setPassword("married");

		dao.add(user);
			
		System.out.println(user.getId() + " 등록 성공");
		
		User user2 = dao.get(user.getId());
		System.out.println(user2.getName());
		System.out.println(user2.getPassword());
			
		System.out.println(user2.getId() + " 조회 성공");
	}
}

```
위의 코드의 문제점을 살펴보면 다음과 같다. 
* 수동 확인 작업의 번거로움
    * `UserDaoTest`는 테스트를 수행하는 과정과 입력 데이터의 준비를 모두 자동으로 진행했지만, 여전히 사람의 눈으로 확인하는 과정이 필요하다. 이는 사람의 책임이므로 자동으로 테스트 된다고 볼 수 없다. 
* 실행 작업의 번거로움
    * `main()` 을 이용해 실행하는 것이 번거롭다. DAO 가 수백 개가 된다고 가정하면 이를 테스트하기 위해 `main()`을 수백번 실행하는 수고가 필요하다

---
## **2. UserDaoTest 개선**

### **JUnit테스트로 전환**
`JUnit`은 테스트 지원 도구로 프레임워크이다. JUnit을 사용하기 위해선 다음과 같은 조건을 만족시켜야 한다. 
1. 메소드가 `public`으로 선언되어야 한다. 
2. `@Test`라는 어노테이션을 붙여야 한다. 

* JUnit에서는 `assertThat()` 이라는 static 메소드를 사용하여 검증할 수 있다. 
    - `assertThat()` 메소드는 첫번째 파라미터 값을 뒤에 나오는 `매처(matcher)` 라고 불리는 조건으로 비교하여 일치하면 다음으로 넘어가고, 아니면 테스트가 실패한다. (기대한 결과값과 다를 경우 `AssertionError`를 발생시킨다.)
    - `매처(matcher)`기능 살펴보기
        - `is()`는 `equals()`와 동일한 비교기능을 한다. 
        - `is(not())` is()에 not()이 붙은 것으로 부정을 의미한다. 

### **JUnit 및 검증코드 으로 전환한 UserDaoTest 코드**
```java
public class UserDaoTest {
	
	@Test 
	public void andAndGet() throws SQLException {
		ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");
		UserDao dao = context.getBean("userDao", UserDao.class);
		
		User user = new User();
		user.setId("gyumee");
		user.setName("박성철");
		user.setPassword("springno1");

		dao.add(user);
			
		User user2 = dao.get(user.getId());
		
		assertThat(user2.getName(), is(user.getName()));
		assertThat(user2.getPassword(), is(user.getPassword()));
	}
	
	public static void main(String[] args) {
		JUnitCore.main("springbook.user.dao.UserDaoTest");
	}
}

```
---
## **3. 개발자를 위한 테스팅 프레임워크 JUnit**
### **테스트 결과의 일관성**
* 테스트는 다음의 조건을 만족해야 한다.
    * 단위테스트는 코드가 바뀌지 않는 한 매번 동일한 결과가 나와야 한다. 
    * DB 에 남아있는 데이터와 외부 환경의 영향을 받지 말아야 한다. 
    * 테스트를 실행하는 순서를 바꿔도 동일한 결과가 보장되어야 한다. 

**동일한 결과를 보장하기 위해 `deleteAll()`과 `addAndGet()` 이 추가된 `UserDaoTest` 클래스**
```java
public class UserDaoTest {
	
	@Test 
	public void andAndGet() throws SQLException {
		ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");
		UserDao dao = context.getBean("userDao", UserDao.class);
		
		dao.deleteAll();
		assertThat(dao.getCount(), is(0));
		
		User user = new User();
		user.setId("gyumee");
		user.setName("박성철");
		user.setPassword("springno1");

		dao.add(user);
		assertThat(dao.getCount(), is(1));
		
		User user2 = dao.get(user.getId());
		
		assertThat(user2.getName(), is(user.getName()));
		assertThat(user2.getPassword(), is(user.getPassword()));
	}
}
```

### **포괄적인 테스트**
* **테스트는 네거티브 테스트를 먼저 만들어라. 에외적인 상황을 빠뜨리지 않고 꼼꼼히 개발하는 것이 좋다.**
--> 항상 테스트하고자 하는 클래스의 메소드의 포괄적인 테스트를 만들어두는 것이 안전하다. 
* `@Test(expected = ~~.class)`를 사용하면 테스트 메소드 실행 중 발생할 것이라 기대되는 예외 클래스를 지정할 수 있다. 지정된 예외를 발생시킬 경우 테스트는 통과하고 예외를 발생시키지 않으면 테스트는 실패한다. 

```java
@Test(expected=EmptyResultDataAccessException.class) // 해당 에러가 발생하면 테스트 성공
public void getUserFailure() throws SQLException {
    dao.deleteAll();
    assertThat(dao.getCount(), is(0));
    
    dao.get("unknown_id"); // 예외를 발생시키는 호출
}
```

### **테스트가 이끄는 개발**
테스트를 먼저 만들어놓고 코드를 짜는 방식의 전략이 존재한다. 

**기능설계를 위한 테스트**
위에 추가한 `getUserFailure()`의 내용을 정리하면 다음과 같다. 
||단계|내용|코드|
|--|--|--|--|
|조건|어떤 조건을 가지고|가져올 사용자 정보가 존재하지 않는 경우에|`dao.deleteAll();`<br>`assertThat(dao.getCount(), is(0));`|
|행위|무엇을 할 때|존재하지 않는 id로 get()을 실행하면|`dao.get("unknown_id");`|
|결과|어떤 결과가 나온다|특별한 예외가 던져진다|`@Test(expected=EmptyResultDataAccessException.class)`|

위의 표처럼 추가하고자 하는 기능을 테스트 코드로 표현해서, 마치 코드로 된 설계문서처럼 미리 만들어놓고, 코드를 수정하여 테스트가 성공하도록 애플리케이션 코드를 계속 다듬는다. 테스트가 성공한다면 코드구현과 테스트 두가지 작업이 동시에 끝나는 것이다. 

**테스트 주도 개발(TDD : Test Driven Development)**
* 만들고자 하는 기능의 내용을 담고 있으면서 코드를 검증도 해줄 수 있도록, 테스트 코드를 먼저 만들고 테스트를 성공하는 코드를 작성하는 방식을 말한다. 
* 기본원칙은 `실패한 테스트를 성공시키기 위한 목적이 아닌 코드는 만들지 않는다` 이다. 
* TDD는 테스트를 작성하고 이를 성공시키는 코드를 만드는 작업의 주기를 가능한 짧게 가져가도록 권장한다. 
* 장점
    * 이 방법은 모든 테스트를 빼먹지 않고 꼼꼼하게 만들 수 있다.
    * 코드를 만들어 테스트를 실행하는 그 사이의 간격이 매우 짧다.

### **테스트 코드 개선**
### **JUnit**
- `@Before` 또는 `@BeforeEach` : `@Test` 메소드가 실행되기 전에 먼저 실행되는 메소드를 정의한다. 
- `@After` 또는 `@AfterEach` : `@Test` 메소드가 실행된 후에 실행되는 메소드를 정의한다. 
- `픽스처(fixture)` : 테스트를 수행하는데 필요한 정보다 오브젝트를 말한다. 
- `JUnit`이 하나의 테스트 클래스를 가져와 테스트를 수행하는 방식은 다음과 같다. 
    1. 테스트 클래스에서 `@Test`가 붙은 `public`이고 `void` 형이며 파라미터가 없는 테스트 메소드를 모두 찾는다. 
    2. 테스트 클래스의 오브젝트를 하나 만든다. 
    3. `@Before` 가 붙은 메소드가 있으면 실행한다. 
    4. `@Test` 가 붙은 메소드를 하나 호출하고 테스트 결과를 저장해둔다. 
    5. `@After` 가 붙은 메소드가 있으면 실행한다. 
    6. 나머지 테스트 메소드에 대해 2~5를 반복한다.
    7. 모든 테스트의 결과를 종합해서 돌려준다.

**User 픽스처를 적용한 UserDaoTest**
```java
public class UserDaoTest {
	private UserDao dao; 
	
	private User user1;
	private User user2;
	private User user3;
	
	@Before
	public void setUp() {
		ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");
		this.dao = context.getBean("userDao", UserDao.class);
		
		this.user1 = new User("gyumee", "박성철", "springno1");
		this.user2 = new User("leegw700", "이길원", "springno2");
		this.user3 = new User("bumjin", "박범진", "springno3");

	}
	...생략
}

```
---
## **4. 스프링 테스트 적용**
### **테스트를 위한 어플리케이션 컨텍스트 관리**
- `@RunWith(SpringJUnit4ClassRunner.class)` : 스프링의 테스트 컨텍스트 프레임워크의 JUnit 확장기능을 지정한다. 
- `@ContextConfiguration(location = "~.xml"` : 테스트 오브젝트가 만들어지고나면 스프링 테스트 컨텍스트에 의해 자동으로 값이 주입된다. 

> **`JUnit`의 확장기능은 테스트가 실행되기 전 딱 한 번만 애플리케이션 컨텍스트를 만들어두고, 테스트 오브젝트가 만들어질 때마다 컨텍스트 자신을 테스트 오브젝트의 특정 필드에 주입해주는 것이다.**

### **@Autowired**
- 스프링의 DI에 사용되는 어노테이션으로 해당 어노테이션이 붙은 인스턴스 변수가 있으면, 테스트 컨텍스트 프레임워크는 변수타입이 동일한 컨텍스트 내의 빈을 찾는다. 
- 일치하는 빈이 있으면 인스턴스 변수에 주입해준다.

> `@Autowired`는 type을 먼저 확인 후 없을 경우 변수명과 동일한 빈이 있는지 확인한다. 이처럼 type으로 빈을 먼저 찾는 것을 타입에 의한 자동 와이어링이라고 부른다. 

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="/applicationContext.xml")
public class UserDaoTest {
	@Autowired
	ApplicationContext context;
	
	...

    @Before
	public void setUp() {
		this.dao = this.context.getBean("userDao", UserDao.class);
	
	... 생략
    }
}

```
* 스프링 애플리케이션 컨텍스트는 초기화할 때 자기자신도 빈으로 등록한다. 그래서 애플리케이션 컨텍스트에는 `ApplicationContext` 타입의 빈이 존재하는 셈이고, `ApplicationContext` DI가 가능한 것이다. 
* 가능한 한 테스트에서도 인터페이스를 통해 애플리케이션 코드와 관계를 느슨하게 해주는 것이 좋다. 
    * why?
        1. 소프트웨어 개발에서 절대로 바뀌지 않는 것은 없다.  --> 변화에 유연하게 대처하기 위해 인터페이스 사용 권장
        2. 클래스의 구현 방식은 변경되지 않는다 하더라도 인터페이스를 두고 DI를 적용하면 다른 차원의 서비스 기능을 도입할 수 있다. 
        3. 테스트를 위해,효율적인 테스트를 손쉽게 만들기 위해서라도 DI를 적용해야 한다. DI는 테스트가 작은 단위의 대상에 대해 독립적으로 만들어지고 실행되게 하는데 중요한 역할을 한다. 

* DI 컨테이너나 프레임워크는 DI를 편하게 적용하도록 도움을 줄 뿐, 컨테이너가 DI를 가능하게 해주는 것은 아니다. 
> 🕵🏻‍♂️ **침투적 기술과 비침투적 기술**<br>
> **침투적 기술**은 **기술을 적용했을 때 애플리케이션 코드에 기술 관련 API가 등장하거나, 특정 인터페이스나 클래스를 사용하도록 강제하는 기술**을 말한다 .
이는 **애플리케이션 코드가 해당 기술에 종속되는 결과**를 가져온다. <br>
> **비침투적 기술**은 **애플리케이션 로직을 담은 코드에 아무런 영향을 주지 않고 적용**이 가능하다. 따라서 **기술에 종속되지 않은 순수한 코드를 유지**할 수 있다.**스프링은 이런 비침투적 기술의 대표적이 예**이다. 그래서 스프링 컨테이너 없는 DI 테스트도 가능하다. 

### **DI를 이용한 테스트 방법 선택**
* 항상 스프링 컨테이너 없이 사용할 수 있는 방법을 가장 우선적으로 고려해라.
* 여러 오브젝트와 의존관계를 갖고있는 오브젝트를 테스트할 때에는 스프링의 설정을 이용한 DI 방식이 편리하다. 
    * 테스트에서 애플리케이션 컨텍스트를 사용하는 경우에는 테스트 전용 설정 파일을 따로 만들어 사용하는 것이 좋다. (개발, 운영 분리)
* 예외적인 의존관계를 강제적으로 구성시엔 수동으로 DI하는 방법을 선택하자. 
    * `@DirtiesContext` 를 붙이는 것을 명심하자

---
## **5. 학습테스트로 배우는 스프링**
* 목적 : 자신이 사용할 API나 프레임워크의 기능을 테스트로 보면서 사용방법을 익히려는 것
* 장점
    1. 다양한 조건에 따른 기능을 손쉽게 확인해볼 수 있다. 
    2. 학습 테스트 코드를 개발 중에 참고할 수 있다. 
    3. 프레임워크나 제품을 업그레이드할 때 호환성 검증을 도와준다. 
    4. 테스트 작성에 대한 좋은 훈련이 된다. 
    5. 새로운 기술을 공부하는 과정이 즐거워진다. 

학습 테스트 예제
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("junit.xml")
public class JUnitTest {
	@Autowired ApplicationContext context;
	
	static Set<JUnitTest> testObjects = new HashSet<JUnitTest>();
	static ApplicationContext contextObject = null;
	
	@Test public void test1() {
		assertThat(testObjects, not(hasItem(this)));
		testObjects.add(this);
		
		assertThat(contextObject == null || contextObject == this.context, is(true));
		contextObject = this.context;
	}
	
	@Test public void test2() {
		assertThat(testObjects, not(hasItem(this)));
		testObjects.add(this);
		
		assertTrue(contextObject == null || contextObject == this.context);
		contextObject = this.context;
	}
	
	@Test public void test3() {
		assertThat(testObjects, not(hasItem(this)));
		testObjects.add(this);
		
		assertThat(contextObject, either(is(nullValue())).or(is(this.contextObject)));
		contextObject = this.context;
	}
}
```
### **버그테스트**
* 버그테스트란 코드에 오류가 있을 때 그 오류를 가장 잘 드러내줄 수 있는 테스트를 말한다. 
* 버그 테스트는 실패하도록 만들어야 한다. 
* 버그 테스트의 필요성과 장점은 다음과 같다. 
    * 테스트의 완성도를 높여준다. 
    * 버그의 내용을 명확하게 분석하게 해준다. 
    * 기술적인 문제를 해결하는데 도움이 된다. 

---
## **정리**
* 테스트는 자동화되어야 하고, 빠르게 실행할 수 있어야 한다. 
* `main()` 테스트 대신 `JUnit` 프레임워크를 이용한 테스트 작성이 편리하다. 
* 테스트 결과는 일관성이 있어야 한다. 코드의 변경 없이 환경이나 테스트 실행 순서에 따라서 결과가 달라지면 안 된다. 
* 테스트는 포괄적으로 작성해야 한다. 충분한 검증을 하지 않는 테스트는 없는 것보다 나쁠 수 있다. 
* 코드 작성과 테스트 수행의 간격이 짧을수록 효과적이다. 
* 테스트를 먼저 만들고 테스트를 성공시키는 코드를 만들어가는 테스트 주도 개발 방법도 유용하다. 
* 테스트 코드도 애플리케이션 코드와 마찬가지로 리팩토링이 필요하다. 
* `@Before`, `@After`를 사용해서 테스트 메소드들의 공통 준비 작업과 정리 작업을 처리할 수 있다. 
* 스프링 테스트 컨텍스트 프레임워크를 이용하면 테스트 성능을 향상시킬 수 있다. 
* 동일한 설정파일을 사용하는 테스트는 하나의 어플리케이션 컨텍스트를 공유한다. 
* `@Autowired`를 사용하면 컨텍스트의 빈을 테스트 오브젝트에 DI 할 수 있다. 
* 기술의 사용방법을 익히고 이해를 돕기 위해 학습 테스트를 작성하자.
* 오류가 발견될 경우 그에 대한 버그 테스트를 만들어두면 유용하다. 


--- 
책은 JUnit4로 설명되었지만, JUnit5로 적용할 경우 코드가 달라지는 부분이 있어서 내용 같이 첨부합니다. 
## **JUnit5 의 Annotations**

|Annotations|설명|
|--|--|
|`@Test`|테스트임을 나타내는 어노테이션입니다.|
|`@BeforeEach`|JUnit4의 `@Before` 어노테이션과 유사하며, 해당 주석이 달린 메소드가 `@Test`, `@RepeatedTest`, `@ParameterizedTest` `@TestFactory` 이전에 실행되어야 함을 알리는 어노테이션입니다. |
|`@AfterEach`|JUnit4의 `@After` 어노테이션과 유사하며, 해당 주석이 달린 메소드가 `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory` 이후에 실행되어야 함을 알리는 어노테이션입니다. |
|`@BeforeAll`|JUnit4의 `@BeforeClass` 어노테이션과 유사하며 해당 주석이 달린 메소드가 `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory` 이전에 실행되어야 함을 알리는 어노테이션입니다. |
|`@AfterAll`|JUnit4의 `@AfterClass`어노테이션과 유사하며 해당 주석이 달린 메소드가 `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory` 이후에 실행되어야 함을 알리는 어노테이션입니다. |
|`@Disabled`|테스트 클래스 또는 테스트 메서드를 비활성화하는 데 사용되며, JUnit 4의 `@Ignore` 어노테이션과 유사합니다.|
|`@ExtendWith`|확장을 선언적으로 등록하는 데 사용됩니다. 스프링을 사용하려면 `@ExtendWith(SpringExtension.class)`를 선언하여 사용합니다. |
|`@DisplayName`|테스트 클래스 또는 테스트 메서드의 사용자가 지정할 이름을 선언합니다.|
|`@TestMethodOrder`|주석이 달린 테스트 클래스에 대한 테스트 방법 실행 순서를 구성하는 데 사용되며, JUnit 4의 `@FixMethodOrder`와 유사합니다. |


* `@Before`, `@After` 가 사라지고, `@BeforeEach` 및 `@AfterEach`로 변경되었습니다. 
* `@BeforeClass`, `@AfterClass`가 사라지고, `@BeforeAll` 및 `@AfterAll`로 변경되었습니다. 
* `@Ignore` 는 더이상 존재하지 않습니다. `@Disabled` 또는 다른 기본 제공 실행 조건 중 하나를 사용하세요.
* `@Category`가 사라지고, `@Tag`로 변경되었습니다.
* `@RunWith`이 사라지고 `@ExtendWith`으로 변경되었습니다.
* `@Rule` 및 `@ClassRule`이 사라지고 `@ExtendWith` 및` @Register`로 변경되었습니다. 