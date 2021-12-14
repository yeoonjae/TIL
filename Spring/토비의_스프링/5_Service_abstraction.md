> 📚 본 글은 토비의 스프링 3.1을 읽고 정리한 글입니다. 

<br>

# **5장 서비스 추상화**

## **5.1 서비스 클래스 추가**
사용자의 활동에 따라서 사용자의 레벨을 부여하는 비즈니스 로직을 추가하려고 한다. 사용자 활동에 따른 레벨은 다음과 같다. 
* 사용자의 레벨은 BASIC, SILVER, GOLD 세 가지 중 하나다. 
* 사용자가 처음 가입하면 BASIC 레벨이 되며, 이후 활동에 따라서 한 단계씩 업그레이드 될 수 있다. 
* 가입 후 50회 이상 로그인을 하면 BASIC에서 SILVER 레벨이 된다. 
* SILVER 레벨에서 30번 이상 추천을 받으면 GOLD 레벨이 된다. 
* 사용자 레벨의 변경 작업은 일정한 주기를 가지고 일괄적으로 진행된다. 변경 작업 전에는 조건을 충족하더라도 레벨의 변경이 일어나지 안흔다. 

다음의 조건을 가지고 사용자에게 레벨을 부여하기 위해 우선 ENUM 클래스를 만들어보자. 
```java
public enum Level {
	GOLD(3, null), SILVER(2, GOLD), BASIC(1, SILVER);  
	
	private final int value;
	private final Level next; 
	
	Level(int value, Level next) {  
		this.value = value;
		this.next = next; 
	}
	
	public int intValue() {
		return value;
	}
	
	public Level nextLevel() { 
		return this.next;
	}
	
	public static Level valueOf(int value) {
		switch(value) {
		case 1: return BASIC;
		case 2: return SILVER;
		case 3: return GOLD;
		default: throw new AssertionError("Unknown value: " + value);
		}
	}
}
```
그리고 User 클래스에도 다음의 필드를 추가해주자.
```java
Level level;
int login;
int recommend;
```

레벨 관리 기능을 구현하기 위해선 사용자의 모든 정보를 조회한 뒤 사용자별로 레벨 업그레이드를 진행하며 DB에 결과를 넣어주면 된다. 하지만 UserDao에서 수행하기엔 적합하지않은 로직이다. 비즈니스 로직 서비스를 제공하는 UserService 클래스를 만들어서 수행하자. 이에 따라 변경되는 ENUM 클래스와 User 클래스의 코드도 변경해주자. 


우선 Enum 클래스인 Level 클래스를 변경해보자. Level과 관계된 현재 레벨의 다음 단계가 무엇인지, 순서와 다음단계정보를 모두 Level Enum에서 관리하게끔 수정해준다.
```java

public enum Level {
	GOLD(3, null), SILVER(2, GOLD), BASIC(1, SILVER);  
	
	private final int value;
	private final Level next; 
	
	Level(int value, Level next) {  
		this.value = value;
		this.next = next; 
	}
	
	public int intValue() {
		return value;
	}
	
	public Level nextLevel() { 
		return this.next;
	}
	
	public static Level valueOf(int value) {
		switch(value) {
		case 1: return BASIC;
		case 2: return SILVER;
		case 3: return GOLD;
		default: throw new AssertionError("Unknown value: " + value);
		}
	}
}
```

그리고 User 클래스에서 level을 호출하여 다음 레벨이 무엇인지 알려달라 요청해서 받은 후 변경하면 된다. 업그레이드가 불가능할 경우엔 예외상황을 던져준다. 
```java
// User 클래스에 UpgradeLevel() 메소드 추가
public void upgradeLevel() {
    Level nextLevel = this.level.nextLevel();	
    if (nextLevel == null) { 								
        throw new IllegalStateException(this.level + "은  업그레이드가 불가능합니다");
    }
    else {
        this.level = nextLevel;
    }	
}
```
이제 UserService를 작성해보자. 
```java
public class UserService {
	public static final int MIN_LOGCOUNT_FOR_SILVER = 50;
	public static final int MIN_RECCOMEND_FOR_GOLD = 30;

	private UserDao userDao;

	public void setUserDao(UserDao userDao) {
		this.userDao = userDao;
	}
	
	public void upgradeLevels() {
		List<User> users = userDao.getAll();  
		for(User user : users) {  
			if (canUpgradeLevel(user)) {  
				upgradeLevel(user);  
			}
		}
	}
	private boolean canUpgradeLevel(User user) {
		Level currentLevel = user.getLevel(); 
		switch(currentLevel) {                                   
		case BASIC: return (user.getLogin() >= MIN_LOGCOUNT_FOR_SILVER); 
		case SILVER: return (user.getRecommend() >= MIN_RECCOMEND_FOR_GOLD);
		case GOLD: return false;
		default: throw new IllegalArgumentException("Unknown Level: " + currentLevel); 
		}
	}

	private void upgradeLevel(User user) {
		user.upgradeLevel();
		userDao.update(user);
	}
	
	public void add(User user) {
		if (user.getLevel() == null) user.setLevel(Level.BASIC);
		userDao.add(user);
	}

}
```
각 오브젝트가 할 책임을 깔끔하게 분리하였고, 간결하고 내용이 명확한 코드로 작성되었다. 

객체지향적인 코드는 다른 오브젝트의 데이터를 가져와서 작업하는 대신 데이터를 갖고 있는 다른 오브젝트에게 작업을 해달라고 요청한다. 오브젝트에게 데이터를 요구하지 말고 작업을 요청하라는 것이 객체지향 프로그래밍의 가장 기본이 되는 원리이기도 하다. 

<u>항상 코드를 더 깔끔하고 유연하면서 변화에 대응하기 쉽고 테스트하기 좋게 만들려고 노력해야함을 기억하자. </u>
> 📝 코드를 작성하고나서는 다음의 질문을 해볼 필요가 있다. 
> * 코드에 중복된 부분은 없는가?
> * 코드가 무엇을 하는 것인지 이해하기 불편하지 않은가?
> * 코드가 자신이 있어야 할 자리에 있는가?
> * 앞으로 변경이 일어난다면 어떤 것이 있을 수 이쏙, 그 변화에 쉽게 대응할 수 있게 작성되어 있는가?

---
## **5.2 트랜젝션 서비스 추상화**
### **트랜젝션**
위의 레벨 관리 작업을 하는 도중에 네트워크가 끊기거나 서버에 장애가 생겨서 작업이 중단된다면 이미 변경이 진행된 작업도 모두 취소시키도록 하고싶다. 

이를 사용하기 위해선 트랜젝션 설정을 해야한다. 트랜잭션이란 더 이상 나눌 수 없는 단위 작업을 말한다. 작업을 쪼개서 작은 단위로 만들 수 없다는 것을 트랜잭션의 핵심 속성인 원자성을 의미한다. 

모든 사용자에 대한 레벨 업그레이드가 모두 성공하든지 또는 모두 실패하든지 해야한다. 이 작업도 <u>**더 이상 쪼개서 이뤄질 수 없는 원자와 같은 성질을 띈다. 따라서 중간에 예외가 발생해서 작업을 완료할 수 없다면 아예 작업이 시작되지 않은 것처럼 초기 상태로 돌려놔야 한다**</u>. 이것이 바로 <u>**트랜잭션**</u>이다. 

두 가지 작업이 하나의 트랜잭션이 되었다고 가정해보자. 성공적으로 첫번째 작업을 마치고 두번째 작업이 완료되지 않았는데 서버오류로 인해 중단이 되었다면 앞서 작업한 첫번째 작업도 작업하기 전 상태로 돌려놓아야 한다. 즉, 취소를 해야한다. 이런 취소작업을 <u>**트랜젝션 롤백(Transaction Rollback)**</u>이라고 한다. 

반대로 두가지 작업이 모두 성공적으로 마쳤을 땐 작업이 모두 성공적으로 마무리 되었다고 알려주어 확정시켜야 한다. 이것을 <u>**트랜잭션 커밋(Trasaction Commit)**</u>이라고 한다. 

트랜잭션의 시작과 종료는 Connection 오브젝트를 통해 이뤄지기 때문에 JDBC에서 트랜잭션을 시작하려면 자동커밋 옵션을 false 로 `c.setAutoCommit(false);` 지정해주면 된다. (기본값은 true) 트랜잭션은 한 번 시작되면 `commit()` 또는 `rollback()` 메소드가 호출될 때까지의 작업이 하나의 트랜잭션으로 묶인다. 

이렇게 `c.setAutoCommit(false);`로 트랜잭션의 시작을 선언하고 `commit()` 또는 `rollback()`을 통해 트랜잭션을 종료하는 작업을 <u>**트랜잭션 경계설정(trasaction demarcation)**</u>이라고 한다. 

* 트랜잭션의 경계는 하나의 Connection이 만들어지고 닫히는 범위 안에 존재한다. 
* DB 커넥션 안에서 만들어지는 트랜잭션을 로컬 트랜잭션이라고도 한다. 

### **트랜잭션 동기화**
🕵🏻‍♂️ <u>**트랜잭션 동기화**</u>란? 비즈니스 로직에서 트랜잭션을 시작하기 위해 만든 Connection 오브젝트를 특별한 저장소에 보관해두고, 이후에 호출되는 DAO 메소드에서는 Connection 오브젝트를 특별한 저장소에 보관해두고, 이후에 호출되는 DAO 의 메소드에서는 저장된 Connection을 가져다가 사용하게 하는 것이다. 

정확히는 <u>DAO 가 사용하는 JdbcTemplate이 트랜잭션 동기화 박식을 이용하도록 하는 것</u>이다. 그리고 트랜잭션이 모두 종료되면 그때는 동기화를 마치면 된다. 

그림으로 살펴보면 다음과 같다. 
![image](https://user-images.githubusercontent.com/63777714/145956503-2083508a-6d8d-4c70-9c80-65c587ac0e1d.png)

### **트랜잭션 동기화 적용**
스프링은 JdbcTemplate과 더불어 이런 트랜잭션 동기화 기능을 지원하는 간단한 유틸리티 메소드를 제공하고 있다. 이를 적용하여 UserService 코드를 변경해보자. 
```java
public class UserService {
	public static final int MIN_LOGCOUNT_FOR_SILVER = 50;
	public static final int MIN_RECCOMEND_FOR_GOLD = 30;

	private UserDao userDao;

	public void setUserDao(UserDao userDao) {
		this.userDao = userDao;
	}
	
    // Connection을 생성할 때 사용한 DataSource를 DI받고록 한다.			
	private DataSource dataSource;  

	public void setDataSource(DataSource dataSource) {
		this.dataSource = dataSource;
	}
	
	public void upgradeLevels() throws Exception {
		TransactionSynchronizationManager.initSynchronization();  // 동기화 관리자를 이용해 동기화 작업 초기화
		Connection c = DataSourceUtils.getConnection(dataSource); // DB 커넥션을 생성하고 
		c.setAutoCommit(false);//트랜잭션 시작, 이후의 DAO 작업은 모두 여기서 사적한 트랜잭션안에서 진행된다. 
		
		try {									   
			List<User> users = userDao.getAll();
			for (User user : users) {
				if (canUpgradeLevel(user)) {
					upgradeLevel(user);
				}
			}
			c.commit();  
		} catch (Exception e) {    
			c.rollback();   // 에외기 빌셍히면 롤백
			throw e;
		} finally {
			DataSourceUtils.releaseConnection(c, dataSource);	// 스프링 유틸리티 메소드를 이용해서 DB 커넥션을 안전하게 닫는다. 
			TransactionSynchronizationManager.unbindResource(this.dataSource);  // 동기화 작업 종료 및 정리
			TransactionSynchronizationManager.clearSynchronization();  
		}
	}
	
	private boolean canUpgradeLevel(User user) {
		Level currentLevel = user.getLevel(); 
		switch(currentLevel) {                                   
		case BASIC: return (user.getLogin() >= MIN_LOGCOUNT_FOR_SILVER); 
		case SILVER: return (user.getRecommend() >= MIN_RECCOMEND_FOR_GOLD);
		case GOLD: return false;
		default: throw new IllegalArgumentException("Unknown Level: " + currentLevel); 
		}
	}

	protected void upgradeLevel(User user) {
		user.upgradeLevel();
		userDao.update(user);
	}
	
	public void add(User user) {
		if (user.getLevel() == null) user.setLevel(Level.BASIC);
		userDao.add(user);
	}
	
}
```

### **기술과 환경에 종속되는 트랜잭션 경계설정 코드**
위에서 작성한 UserService는 문제점이 있다. 바로 여러 개의 DB가 참여하는 작업을 하나의 트랜잭션으로 만들 수 없다는 것이다. 위의 코드는 로컬 트랜잭션을 사용했기 때문에 하나의 DB에 종속된다. 여러개의 DB를 사용하려면 글로벌 트랜잭션 방식을 사용해야 한다. 

자바는 JDBC 외에 이런 글로벌 트랜잭션을 지원하는 트랜잭션 매니저를 지원하기 위한 API인 JTA(Java Transaction API)를 제공한다. 이를 사용하면 글로벌 트랜잭션이 가능하다. 

그러기 위해선 UserService의 코드를 직접 바꿔야한다. 또한, 만약 하이버네이트를 이용해 UserDao를 직접 구현하는 경우엔 위에서 만든 UserService 코드를 사용하지 못한다. UserService와 UserDao는 DI를 통해 연결되어 있기 때문에 UserService를 수정이 필요한 상황이다. 

즉, 위의 코드는 기술 또는 환경에 종속된 코드인 것이다. 

UserService 코드가 특정 트랜잭션 방법에 의존적이지 않고 독립적일 수 있게 만들려면 어떻게 해야할까? 바로 트랜잭션 처리 코드에도 추상화를 도입하는 방법이 있다. JDBC, JPA, JTA, 하이버네이트 모두 트랜잭션이라는 공통적인 특징이 있으니 트랜잭션 관리 계층을 만들 수 있다. 

스프링은 트랜잭션 기술의 공통점을 담은 트랜잭션 추상화 기술을 제공하고 있다. 이를 이용해서 UserService를 수정해보자.

![image](https://user-images.githubusercontent.com/63777714/145963357-f1e2e762-4dd6-4ca7-b3a8-43d84c7d53c9.png)


```java
public class UserService {
	public static final int MIN_LOGCOUNT_FOR_SILVER = 50;
	public static final int MIN_RECCOMEND_FOR_GOLD = 30;

	private UserDao userDao;
	private MailSender mailSender;
	private PlatformTransactionManager transactionManager;

	public void setUserDao(UserDao userDao) {
		this.userDao = userDao;
	}

	public void setTransactionManager(PlatformTransactionManager transactionManager) {
		this.transactionManager = transactionManager;
	}

	public void upgradeLevels() {
		TransactionStatus status = 
            //DI받은 트랜잭션 매니저를 공유해서 사용한다. 멀티스레드 환경에서도 안전함
			this.transactionManager.getTransaction(new DefaultTransactionDefinition());
		try {
			List<User> users = userDao.getAll();
			for (User user : users) {
				if (canUpgradeLevel(user)) {
					upgradeLevel(user);
				}
			}
			this.transactionManager.commit(status);
		} catch (RuntimeException e) {
			this.transactionManager.rollback(status);
			throw e;
		}
	}
	
	private boolean canUpgradeLevel(User user) {
		Level currentLevel = user.getLevel(); 
		switch(currentLevel) {                                   
		case BASIC: return (user.getLogin() >= MIN_LOGCOUNT_FOR_SILVER); 
		case SILVER: return (user.getRecommend() >= MIN_RECCOMEND_FOR_GOLD);
		case GOLD: return false;
		default: throw new IllegalArgumentException("Unknown Level: " + currentLevel); 
		}
	}

	protected void upgradeLevel(User user) {
		user.upgradeLevel();
		userDao.update(user);
		sendUpgradeEMail(user);
	}
	
	public void add(User user) {
		if (user.getLevel() == null) user.setLevel(Level.BASIC);
		userDao.add(user);
	}
}
```
트랜잭션 관련 기능이 필요하다면 DI를 받은 인스턴스 변수를 통해 사용가능하도록 UserService의 코드를 변경했다. 모두 PlatformTransactionManager 인터페이스를 구현한 것이니 코드는 문제없이 돌아갈 것이다. (DI를 받았으니 빈으로 등록해주는 것을 잊지말자.) 또한, 인터페이스를 통한 추상화 게층을 사이에 두고 사용함으로써 구체적인 트랜잭션 기술에도 독립적인 코드라 할 수 있다. 

---

위에서 작성한 UserService와 이전에 작성했던 UserDao 모두 결합도는 낮고 의존도는 높은, 서로 독립적이면서 확장될 수 있는 코드이다. 

이처럼 서로 영향을 주지 않고 자유롭게 확장될 수 있는 구조를 만들 수 있는데는 스프링의 DI가 중요한 역할을 하고 있다. <u>DI의 가치는 이렇게 관심, 책임, 성격이 다른 코드를 깔끔하게 분리하는데 있다.</u>

### **단일 책임 원칙**
이런 적작한 분리가 가져오는 특징은 객체지향 설계의 원칙 중 하나인 단일 책임 원칙(Single Responsibility Principle)으로 설명할 수 있다. 단일 책임 원칙은 하나의 모듈은 한 가지 책임을 가져야 한다는 의미이다. 하나의 모듈이 바뀌는 이유는 한 가지여야 한다고 설명할 수도 있다.

따라서, 적절하게 책임과 관심이 다른 코드를 분리하고, 서로 영향을 주지 않도록 다양한 추상화 기법을 도입하고, 애플리케이션 로직과 기술/환경을 분리하는 등의 작업은 갈수록 복잡해지는 엔터프라이즈 환경에는 반드시 필요하다. 

### **단일 책임 원칙의 장점**
* 단일 책임 원칙을 잘 지키고 있다면, 어떤 변경이 필요할 때 수정 대상이 명확해진다. 
* 단일 책임 원칙을 잘 지키는 코드를 만들려면 인터페이스를 도입하고 이를 DI로 연결해야 하며
* 그 결과로 단일 책임 원칙 뿐 아니라 개방 폐쇄 원칙도 잘 지키고, 모듈간에 결합도가 낮아서 서로의 변경이 영향을 주지 않는다. 
* 같은 이유로 변경이 단일 책임에 집중되는 응집도 높은 코드가 나온다. 
* 이 과정에서 전략 패턴, 어댑터 패턴, 브리지 패턴, 미디에이터 패턴 등 많은 디자인 패턴이 자연스럽게 적용되기도 한다. 
* DI 는 모든 스프링 기술의 기반이 되는 핵심 엔진이자 원리이며, 스프링이 지지하고 지원하는, 좋은 설계와 코드를 만드는 모든 과정에서 사용되는 가장 중요한 도구이다. 


---
## **5.3 메일 서비스 추상화 (작성중)**
사용자 레벨 관리에 대해 레벨이 업그레이드되는 사용자에게는 아내 메일을 발송하는 기능을 추가해보자. 

```java

```
--- 
## **정리**
* 비즈니스 로직을 담은 코드는 데이터엑세스 로직을 담은 코드와 깔끔하게 분리되는 것이 바람직하다. 비스니스 로직 코드 또한 내부적으로 책임과 역할에 따라서 깔끔하게 메소드로 정리되어야 한다. 
* 이를 위해서는 DAO의 기술 변화에 서비스 계층의 코드가 영향을 받지 않도록 인터페이스와 DI를 잘 활용해서 결합도를 낮춰줘야 하낟.
* DAO를 사용하는 비즈니스 로직에는 단위 작업을 보장해주는 트랜잭션이 필요하다. 
* 트랜잭션의 시작과 종료를 지정하는 일을 트랜잭션 경계 설정이라고 한다. 트랜잭션 경계설정은 주로 비즈니스 로직 안에서 일어나는 경우가 많다. 
* 시작된 트랜잭션 정보를 담은 오브젝트를 파라미터로 DAO에 전달하는 방법은 매우 비효율적이기 때문에 스프링이 제공하는 트랜잭션 동기화 기법을 활용하는 것이 편리하다. 
* 자바에서 사용되는 트랜잭션 API의 종류와 방법은 다양하다. 환경과 서버에 따라서 트랜잭션 방법이 변경되면 경계설정 코드도 함께 변경되어야 한다. 
* 트랜잭션 방법에 따라 비즈니스 로직을 담은 코드가 함께 변경되면 단일 책임 원칙에 위배되며, DAO 가 사용하는 특정 기술에 대해 강한 결합을 만들어낸다. 
* 트랜잭션 경계설정 코드가 비즈니스 로직 코드에 영향을 주지 않게 하려면 스프링이 제공하는 트랜잭션 서비스 추상화를 이용하면 된다. 
* 서비스 추상화는 로우 레벨의 트랜잭션 기술과 API의 변화에 상관없이 일관된 API를 가진 추상화 계층을 도입한다. 
* 서비스 추상화는 테스트하기 어려운 JavaMail 같은 기술에도 적용할 수 있다. 테스트를 편리하게 작성하도록 도와주는 것만으로도 서비스 추상화는 가치가 있다. 
* 테스트 대상이 사용하는 의존 오브젝트를 대체할 수 있도록 만든 오브젝트를 테스트 대역이라고 한다. 
* 테스트 대역은 테스트 대상 오브젝트가 원활하게 동작할 수 있도록 도우며서 테스트를 위해 간접적인 정보를 제공해주기도 한다.
* 테스트 대역 중에서 테스트 대상으로부터 전달받은 정보를 검증할 수 있도록 설계된 것을 목 오브젝트라고 한다. 


















