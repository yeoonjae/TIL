> π λ³Έ κΈμ ν λΉμ μ€νλ§ 3.1μ μ½κ³  μ λ¦¬ν κΈμλλ€. 

<br>

# **5μ₯ μλΉμ€ μΆμν**

## **5.1 μλΉμ€ ν΄λμ€ μΆκ°**
μ¬μ©μμ νλμ λ°λΌμ μ¬μ©μμ λ λ²¨μ λΆμ¬νλ λΉμ¦λμ€ λ‘μ§μ μΆκ°νλ €κ³  νλ€. μ¬μ©μ νλμ λ°λ₯Έ λ λ²¨μ λ€μκ³Ό κ°λ€. 
* μ¬μ©μμ λ λ²¨μ BASIC, SILVER, GOLD μΈ κ°μ§ μ€ νλλ€. 
* μ¬μ©μκ° μ²μ κ°μνλ©΄ BASIC λ λ²¨μ΄ λλ©°, μ΄ν νλμ λ°λΌμ ν λ¨κ³μ© μκ·Έλ μ΄λ λ  μ μλ€. 
* κ°μ ν 50ν μ΄μ λ‘κ·ΈμΈμ νλ©΄ BASICμμ SILVER λ λ²¨μ΄ λλ€. 
* SILVER λ λ²¨μμ 30λ² μ΄μ μΆμ²μ λ°μΌλ©΄ GOLD λ λ²¨μ΄ λλ€. 
* μ¬μ©μ λ λ²¨μ λ³κ²½ μμμ μΌμ ν μ£ΌκΈ°λ₯Ό κ°μ§κ³  μΌκ΄μ μΌλ‘ μ§νλλ€. λ³κ²½ μμ μ μλ μ‘°κ±΄μ μΆ©μ‘±νλλΌλ λ λ²¨μ λ³κ²½μ΄ μΌμ΄λμ§ μνλ€. 

λ€μμ μ‘°κ±΄μ κ°μ§κ³  μ¬μ©μμκ² λ λ²¨μ λΆμ¬νκΈ° μν΄ μ°μ  ENUM ν΄λμ€λ₯Ό λ§λ€μ΄λ³΄μ. 
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
κ·Έλ¦¬κ³  User ν΄λμ€μλ λ€μμ νλλ₯Ό μΆκ°ν΄μ£Όμ.
```java
Level level;
int login;
int recommend;
```

λ λ²¨ κ΄λ¦¬ κΈ°λ₯μ κ΅¬ννκΈ° μν΄μ  μ¬μ©μμ λͺ¨λ  μ λ³΄λ₯Ό μ‘°νν λ€ μ¬μ©μλ³λ‘ λ λ²¨ μκ·Έλ μ΄λλ₯Ό μ§ννλ©° DBμ κ²°κ³Όλ₯Ό λ£μ΄μ£Όλ©΄ λλ€. νμ§λ§ UserDaoμμ μννκΈ°μ μ ν©νμ§μμ λ‘μ§μ΄λ€. λΉμ¦λμ€ λ‘μ§ μλΉμ€λ₯Ό μ κ³΅νλ UserService ν΄λμ€λ₯Ό λ§λ€μ΄μ μννμ. μ΄μ λ°λΌ λ³κ²½λλ ENUM ν΄λμ€μ User ν΄λμ€μ μ½λλ λ³κ²½ν΄μ£Όμ. 


μ°μ  Enum ν΄λμ€μΈ Level ν΄λμ€λ₯Ό λ³κ²½ν΄λ³΄μ. Levelκ³Ό κ΄κ³λ νμ¬ λ λ²¨μ λ€μ λ¨κ³κ° λ¬΄μμΈμ§, μμμ λ€μλ¨κ³μ λ³΄λ₯Ό λͺ¨λ Level Enumμμ κ΄λ¦¬νκ²λ μμ ν΄μ€λ€.
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

κ·Έλ¦¬κ³  User ν΄λμ€μμ levelμ νΈμΆνμ¬ λ€μ λ λ²¨μ΄ λ¬΄μμΈμ§ μλ €λ¬λΌ μμ²­ν΄μ λ°μ ν λ³κ²½νλ©΄ λλ€. μκ·Έλ μ΄λκ° λΆκ°λ₯ν  κ²½μ°μ μμΈμν©μ λμ Έμ€λ€. 
```java
// User ν΄λμ€μ UpgradeLevel() λ©μλ μΆκ°
public void upgradeLevel() {
    Level nextLevel = this.level.nextLevel();	
    if (nextLevel == null) { 								
        throw new IllegalStateException(this.level + "μ  μκ·Έλ μ΄λκ° λΆκ°λ₯ν©λλ€");
    }
    else {
        this.level = nextLevel;
    }	
}
```
μ΄μ  UserServiceλ₯Ό μμ±ν΄λ³΄μ. 
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
κ° μ€λΈμ νΈκ° ν  μ±μμ κΉλνκ² λΆλ¦¬νμκ³ , κ°κ²°νκ³  λ΄μ©μ΄ λͺνν μ½λλ‘ μμ±λμλ€. 

κ°μ²΄μ§ν₯μ μΈ μ½λλ λ€λ₯Έ μ€λΈμ νΈμ λ°μ΄ν°λ₯Ό κ°μ Έμμ μμνλ λμ  λ°μ΄ν°λ₯Ό κ°κ³  μλ λ€λ₯Έ μ€λΈμ νΈμκ² μμμ ν΄λ¬λΌκ³  μμ²­νλ€. μ€λΈμ νΈμκ² λ°μ΄ν°λ₯Ό μκ΅¬νμ§ λ§κ³  μμμ μμ²­νλΌλ κ²μ΄ κ°μ²΄μ§ν₯ νλ‘κ·Έλλ°μ κ°μ₯ κΈ°λ³Έμ΄ λλ μλ¦¬μ΄κΈ°λ νλ€. 

<u>ν­μ μ½λλ₯Ό λ κΉλνκ³  μ μ°νλ©΄μ λ³νμ λμνκΈ° μ½κ³  νμ€νΈνκΈ° μ’κ² λ§λ€λ €κ³  λΈλ ₯ν΄μΌν¨μ κΈ°μ΅νμ. </u>
> π μ½λλ₯Ό μμ±νκ³ λμλ λ€μμ μ§λ¬Έμ ν΄λ³Ό νμκ° μλ€. 
> * μ½λμ μ€λ³΅λ λΆλΆμ μλκ°?
> * μ½λκ° λ¬΄μμ νλ κ²μΈμ§ μ΄ν΄νκΈ° λΆνΈνμ§ μμκ°?
> * μ½λκ° μμ μ΄ μμ΄μΌ ν  μλ¦¬μ μλκ°?
> * μμΌλ‘ λ³κ²½μ΄ μΌμ΄λλ€λ©΄ μ΄λ€ κ²μ΄ μμ μ μ΄μ, κ·Έ λ³νμ μ½κ² λμν  μ μκ² μμ±λμ΄ μλκ°?

---
## **5.2 νΈλμ μ μλΉμ€ μΆμν**
### **νΈλμ μ**
μμ λ λ²¨ κ΄λ¦¬ μμμ νλ λμ€μ λ€νΈμν¬κ° λκΈ°κ±°λ μλ²μ μ₯μ κ° μκ²¨μ μμμ΄ μ€λ¨λλ€λ©΄ μ΄λ―Έ λ³κ²½μ΄ μ§νλ μμλ λͺ¨λ μ·¨μμν€λλ‘ νκ³ μΆλ€. 

μ΄λ₯Ό μ¬μ©νκΈ° μν΄μ  νΈλμ μ μ€μ μ ν΄μΌνλ€. νΈλμ­μμ΄λ λ μ΄μ λλ μ μλ λ¨μ μμμ λ§νλ€. μμμ μͺΌκ°μ μμ λ¨μλ‘ λ§λ€ μ μλ€λ κ²μ νΈλμ­μμ ν΅μ¬ μμ±μΈ μμμ±μ μλ―Ένλ€. 

λͺ¨λ  μ¬μ©μμ λν λ λ²¨ μκ·Έλ μ΄λκ° λͺ¨λ μ±κ³΅νλ μ§ λλ λͺ¨λ μ€ν¨νλ μ§ ν΄μΌνλ€. μ΄ μμλ <u>**λ μ΄μ μͺΌκ°μ μ΄λ€μ§ μ μλ μμμ κ°μ μ±μ§μ λλ€. λ°λΌμ μ€κ°μ μμΈκ° λ°μν΄μ μμμ μλ£ν  μ μλ€λ©΄ μμ μμμ΄ μμλμ§ μμ κ²μ²λΌ μ΄κΈ° μνλ‘ λλ €λμΌ νλ€**</u>. μ΄κ²μ΄ λ°λ‘ <u>**νΈλμ­μ**</u>μ΄λ€. 

λ κ°μ§ μμμ΄ νλμ νΈλμ­μμ΄ λμλ€κ³  κ°μ ν΄λ³΄μ. μ±κ³΅μ μΌλ‘ μ²«λ²μ§Έ μμμ λ§μΉκ³  λλ²μ§Έ μμμ΄ μλ£λμ§ μμλλ° μλ²μ€λ₯λ‘ μΈν΄ μ€λ¨μ΄ λμλ€λ©΄ μμ μμν μ²«λ²μ§Έ μμλ μμνκΈ° μ  μνλ‘ λλ €λμμΌ νλ€. μ¦, μ·¨μλ₯Ό ν΄μΌνλ€. μ΄λ° μ·¨μμμμ <u>**νΈλμ μ λ‘€λ°±(Transaction Rollback)**</u>μ΄λΌκ³  νλ€. 

λ°λλ‘ λκ°μ§ μμμ΄ λͺ¨λ μ±κ³΅μ μΌλ‘ λ§μ³€μ λ μμμ΄ λͺ¨λ μ±κ³΅μ μΌλ‘ λ§λ¬΄λ¦¬ λμλ€κ³  μλ €μ£Όμ΄ νμ μμΌμΌ νλ€. μ΄κ²μ <u>**νΈλμ­μ μ»€λ°(Trasaction Commit)**</u>μ΄λΌκ³  νλ€. 

νΈλμ­μμ μμκ³Ό μ’λ£λ Connection μ€λΈμ νΈλ₯Ό ν΅ν΄ μ΄λ€μ§κΈ° λλ¬Έμ JDBCμμ νΈλμ­μμ μμνλ €λ©΄ μλμ»€λ° μ΅μμ false λ‘ `c.setAutoCommit(false);` μ§μ ν΄μ£Όλ©΄ λλ€. (κΈ°λ³Έκ°μ true) νΈλμ­μμ ν λ² μμλλ©΄ `commit()` λλ `rollback()` λ©μλκ° νΈμΆλ  λκΉμ§μ μμμ΄ νλμ νΈλμ­μμΌλ‘ λ¬ΆμΈλ€. 

μ΄λ κ² `c.setAutoCommit(false);`λ‘ νΈλμ­μμ μμμ μ μΈνκ³  `commit()` λλ `rollback()`μ ν΅ν΄ νΈλμ­μμ μ’λ£νλ μμμ <u>**νΈλμ­μ κ²½κ³μ€μ (trasaction demarcation)**</u>μ΄λΌκ³  νλ€. 

* νΈλμ­μμ κ²½κ³λ νλμ Connectionμ΄ λ§λ€μ΄μ§κ³  λ«νλ λ²μ μμ μ‘΄μ¬νλ€. 
* DB μ»€λ₯μ μμμ λ§λ€μ΄μ§λ νΈλμ­μμ λ‘μ»¬ νΈλμ­μμ΄λΌκ³ λ νλ€. 

### **νΈλμ­μ λκΈ°ν**
π΅π»ββοΈ <u>**νΈλμ­μ λκΈ°ν**</u>λ? λΉμ¦λμ€ λ‘μ§μμ νΈλμ­μμ μμνκΈ° μν΄ λ§λ  Connection μ€λΈμ νΈλ₯Ό νΉλ³ν μ μ₯μμ λ³΄κ΄ν΄λκ³ , μ΄νμ νΈμΆλλ DAO λ©μλμμλ Connection μ€λΈμ νΈλ₯Ό νΉλ³ν μ μ₯μμ λ³΄κ΄ν΄λκ³ , μ΄νμ νΈμΆλλ DAO μ λ©μλμμλ μ μ₯λ Connectionμ κ°μ Έλ€κ° μ¬μ©νκ² νλ κ²μ΄λ€. 

μ ννλ <u>DAO κ° μ¬μ©νλ JdbcTemplateμ΄ νΈλμ­μ λκΈ°ν λ°μμ μ΄μ©νλλ‘ νλ κ²</u>μ΄λ€. κ·Έλ¦¬κ³  νΈλμ­μμ΄ λͺ¨λ μ’λ£λλ©΄ κ·Έλλ λκΈ°νλ₯Ό λ§μΉλ©΄ λλ€. 

κ·Έλ¦ΌμΌλ‘ μ΄ν΄λ³΄λ©΄ λ€μκ³Ό κ°λ€. 
![image](https://user-images.githubusercontent.com/63777714/145956503-2083508a-6d8d-4c70-9c80-65c587ac0e1d.png)

### **νΈλμ­μ λκΈ°ν μ μ©**
μ€νλ§μ JdbcTemplateκ³Ό λλΆμ΄ μ΄λ° νΈλμ­μ λκΈ°ν κΈ°λ₯μ μ§μνλ κ°λ¨ν μ νΈλ¦¬ν° λ©μλλ₯Ό μ κ³΅νκ³  μλ€. μ΄λ₯Ό μ μ©νμ¬ UserService μ½λλ₯Ό λ³κ²½ν΄λ³΄μ. 
```java
public class UserService {
	public static final int MIN_LOGCOUNT_FOR_SILVER = 50;
	public static final int MIN_RECCOMEND_FOR_GOLD = 30;

	private UserDao userDao;

	public void setUserDao(UserDao userDao) {
		this.userDao = userDao;
	}
	
    // Connectionμ μμ±ν  λ μ¬μ©ν DataSourceλ₯Ό DIλ°κ³ λ‘ νλ€.			
	private DataSource dataSource;  

	public void setDataSource(DataSource dataSource) {
		this.dataSource = dataSource;
	}
	
	public void upgradeLevels() throws Exception {
		TransactionSynchronizationManager.initSynchronization();  // λκΈ°ν κ΄λ¦¬μλ₯Ό μ΄μ©ν΄ λκΈ°ν μμ μ΄κΈ°ν
		Connection c = DataSourceUtils.getConnection(dataSource); // DB μ»€λ₯μμ μμ±νκ³  
		c.setAutoCommit(false);//νΈλμ­μ μμ, μ΄νμ DAO μμμ λͺ¨λ μ¬κΈ°μ μ¬μ ν νΈλμ­μμμμ μ§νλλ€. 
		
		try {									   
			List<User> users = userDao.getAll();
			for (User user : users) {
				if (canUpgradeLevel(user)) {
					upgradeLevel(user);
				}
			}
			c.commit();  
		} catch (Exception e) {    
			c.rollback();   // μμΈκΈ° λΉμνλ©΄ λ‘€λ°±
			throw e;
		} finally {
			DataSourceUtils.releaseConnection(c, dataSource);	// μ€νλ§ μ νΈλ¦¬ν° λ©μλλ₯Ό μ΄μ©ν΄μ DB μ»€λ₯μμ μμ νκ² λ«λλ€. 
			TransactionSynchronizationManager.unbindResource(this.dataSource);  // λκΈ°ν μμ μ’λ£ λ° μ λ¦¬
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

### **κΈ°μ κ³Ό νκ²½μ μ’μλλ νΈλμ­μ κ²½κ³μ€μ  μ½λ**
μμμ μμ±ν UserServiceλ λ¬Έμ μ μ΄ μλ€. λ°λ‘ μ¬λ¬ κ°μ DBκ° μ°Έμ¬νλ μμμ νλμ νΈλμ­μμΌλ‘ λ§λ€ μ μλ€λ κ²μ΄λ€. μμ μ½λλ λ‘μ»¬ νΈλμ­μμ μ¬μ©νκΈ° λλ¬Έμ νλμ DBμ μ’μλλ€. μ¬λ¬κ°μ DBλ₯Ό μ¬μ©νλ €λ©΄ κΈλ‘λ² νΈλμ­μ λ°©μμ μ¬μ©ν΄μΌ νλ€. 

μλ°λ JDBC μΈμ μ΄λ° κΈλ‘λ² νΈλμ­μμ μ§μνλ νΈλμ­μ λ§€λμ λ₯Ό μ§μνκΈ° μν APIμΈ JTA(Java Transaction API)λ₯Ό μ κ³΅νλ€. μ΄λ₯Ό μ¬μ©νλ©΄ κΈλ‘λ² νΈλμ­μμ΄ κ°λ₯νλ€. 

κ·Έλ¬κΈ° μν΄μ  UserServiceμ μ½λλ₯Ό μ§μ  λ°κΏμΌνλ€. λν, λ§μ½ νμ΄λ²λ€μ΄νΈλ₯Ό μ΄μ©ν΄ UserDaoλ₯Ό μ§μ  κ΅¬ννλ κ²½μ°μ μμμ λ§λ  UserService μ½λλ₯Ό μ¬μ©νμ§ λͺ»νλ€. UserServiceμ UserDaoλ DIλ₯Ό ν΅ν΄ μ°κ²°λμ΄ μκΈ° λλ¬Έμ UserServiceλ₯Ό μμ μ΄ νμν μν©μ΄λ€. 

μ¦, μμ μ½λλ κΈ°μ  λλ νκ²½μ μ’μλ μ½λμΈ κ²μ΄λ€. 

UserService μ½λκ° νΉμ  νΈλμ­μ λ°©λ²μ μμ‘΄μ μ΄μ§ μκ³  λλ¦½μ μΌ μ μκ² λ§λ€λ €λ©΄ μ΄λ»κ² ν΄μΌν κΉ? λ°λ‘ νΈλμ­μ μ²λ¦¬ μ½λμλ μΆμνλ₯Ό λμνλ λ°©λ²μ΄ μλ€. JDBC, JPA, JTA, νμ΄λ²λ€μ΄νΈ λͺ¨λ νΈλμ­μμ΄λΌλ κ³΅ν΅μ μΈ νΉμ§μ΄ μμΌλ νΈλμ­μ κ΄λ¦¬ κ³μΈ΅μ λ§λ€ μ μλ€. 

μ€νλ§μ νΈλμ­μ κΈ°μ μ κ³΅ν΅μ μ λ΄μ νΈλμ­μ μΆμν κΈ°μ μ μ κ³΅νκ³  μλ€. μ΄λ₯Ό μ΄μ©ν΄μ UserServiceλ₯Ό μμ ν΄λ³΄μ.

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
            //DIλ°μ νΈλμ­μ λ§€λμ λ₯Ό κ³΅μ ν΄μ μ¬μ©νλ€. λ©ν°μ€λ λ νκ²½μμλ μμ ν¨
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
νΈλμ­μ κ΄λ ¨ κΈ°λ₯μ΄ νμνλ€λ©΄ DIλ₯Ό λ°μ μΈμ€ν΄μ€ λ³μλ₯Ό ν΅ν΄ μ¬μ©κ°λ₯νλλ‘ UserServiceμ μ½λλ₯Ό λ³κ²½νλ€. λͺ¨λ PlatformTransactionManager μΈν°νμ΄μ€λ₯Ό κ΅¬νν κ²μ΄λ μ½λλ λ¬Έμ μμ΄ λμκ° κ²μ΄λ€. (DIλ₯Ό λ°μμΌλ λΉμΌλ‘ λ±λ‘ν΄μ£Όλ κ²μ μμ§λ§μ.) λν, μΈν°νμ΄μ€λ₯Ό ν΅ν μΆμν κ²μΈ΅μ μ¬μ΄μ λκ³  μ¬μ©ν¨μΌλ‘μ¨ κ΅¬μ²΄μ μΈ νΈλμ­μ κΈ°μ μλ λλ¦½μ μΈ μ½λλΌ ν  μ μλ€. 

---

μμμ μμ±ν UserServiceμ μ΄μ μ μμ±νλ UserDao λͺ¨λ κ²°ν©λλ λ?κ³  μμ‘΄λλ λμ, μλ‘ λλ¦½μ μ΄λ©΄μ νμ₯λ  μ μλ μ½λμ΄λ€. 

μ΄μ²λΌ μλ‘ μν₯μ μ£Όμ§ μκ³  μμ λ‘­κ² νμ₯λ  μ μλ κ΅¬μ‘°λ₯Ό λ§λ€ μ μλλ°λ μ€νλ§μ DIκ° μ€μν μ­ν μ νκ³  μλ€. <u>DIμ κ°μΉλ μ΄λ κ² κ΄μ¬, μ±μ, μ±κ²©μ΄ λ€λ₯Έ μ½λλ₯Ό κΉλνκ² λΆλ¦¬νλλ° μλ€.</u>

### **λ¨μΌ μ±μ μμΉ**
μ΄λ° μ μ ν λΆλ¦¬κ° κ°μ Έμ€λ νΉμ§μ κ°μ²΄μ§ν₯ μ€κ³μ μμΉ μ€ νλμΈ λ¨μΌ μ±μ μμΉ(Single Responsibility Principle)μΌλ‘ μ€λͺν  μ μλ€. λ¨μΌ μ±μ μμΉμ νλμ λͺ¨λμ ν κ°μ§ μ±μμ κ°μ ΈμΌ νλ€λ μλ―Έμ΄λ€. νλμ λͺ¨λμ΄ λ°λλ μ΄μ λ ν κ°μ§μ¬μΌ νλ€κ³  μ€λͺν  μλ μλ€.

λ°λΌμ, μ μ νκ² μ±μκ³Ό κ΄μ¬μ΄ λ€λ₯Έ μ½λλ₯Ό λΆλ¦¬νκ³ , μλ‘ μν₯μ μ£Όμ§ μλλ‘ λ€μν μΆμν κΈ°λ²μ λμνκ³ , μ νλ¦¬μΌμ΄μ λ‘μ§κ³Ό κΈ°μ /νκ²½μ λΆλ¦¬νλ λ±μ μμμ κ°μλ‘ λ³΅μ‘ν΄μ§λ μν°νλΌμ΄μ¦ νκ²½μλ λ°λμ νμνλ€. 

### **λ¨μΌ μ±μ μμΉμ μ₯μ **
* λ¨μΌ μ±μ μμΉμ μ μ§ν€κ³  μλ€λ©΄, μ΄λ€ λ³κ²½μ΄ νμν  λ μμ  λμμ΄ λͺνν΄μ§λ€. 
* λ¨μΌ μ±μ μμΉμ μ μ§ν€λ μ½λλ₯Ό λ§λ€λ €λ©΄ μΈν°νμ΄μ€λ₯Ό λμνκ³  μ΄λ₯Ό DIλ‘ μ°κ²°ν΄μΌ νλ©°
* κ·Έ κ²°κ³Όλ‘ λ¨μΌ μ±μ μμΉ λΏ μλλΌ κ°λ°© νμ μμΉλ μ μ§ν€κ³ , λͺ¨λκ°μ κ²°ν©λκ° λ?μμ μλ‘μ λ³κ²½μ΄ μν₯μ μ£Όμ§ μλλ€. 
* κ°μ μ΄μ λ‘ λ³κ²½μ΄ λ¨μΌ μ±μμ μ§μ€λλ μμ§λ λμ μ½λκ° λμ¨λ€. 
* μ΄ κ³Όμ μμ μ λ΅ ν¨ν΄, μ΄λν° ν¨ν΄, λΈλ¦¬μ§ ν¨ν΄, λ―Έλμμ΄ν° ν¨ν΄ λ± λ§μ λμμΈ ν¨ν΄μ΄ μμ°μ€λ½κ² μ μ©λκΈ°λ νλ€. 
* DI λ λͺ¨λ  μ€νλ§ κΈ°μ μ κΈ°λ°μ΄ λλ ν΅μ¬ μμ§μ΄μ μλ¦¬μ΄λ©°, μ€νλ§μ΄ μ§μ§νκ³  μ§μνλ, μ’μ μ€κ³μ μ½λλ₯Ό λ§λλ λͺ¨λ  κ³Όμ μμ μ¬μ©λλ κ°μ₯ μ€μν λκ΅¬μ΄λ€. 


---
## **5.3 λ©μΌ μλΉμ€ μΆμν**
μ¬μ©μ λ λ²¨ κ΄λ¦¬μ λν΄ λ λ²¨μ΄ μκ·Έλ μ΄λλλ μ¬μ©μμκ²λ μλ΄ λ©μΌμ λ°μ‘νλ κΈ°λ₯μ μΆκ°ν΄λ³΄μ. 

μλ°κ° μ κ³΅νμ£Όλ javax.mail ν¨ν€μ§μ μλ JavaMailμ μ΄μ©ν΄μ κΈ°λ₯μ μΆκ°ν΄λ³΄μ. μ°μ  Userμ email νλλ₯Ό μΆκ°νκ³  μ΄λ©μΌμ λ°μ‘νλ κΈ°λ₯μ λ£μ. 
```java
// JavaMialμ μ΄μ©ν μ΄λ©μΌ λ°μ‘ λ©μλ
private coid sendUpgradeEMail (User user) {
	Properties props = new Properties();
	props.put("mail.smtp.host", "mail.ksug.org");
	Session s = Session.getInstance(props, null);

	MimeMessage message = new MimeMessage(s);
	try {
		message.setFrom(new InternetAddress("useradmin@ksug.org"));
		message.addRecipient(Message.RecipientType.TO, new InternetAddress(user.getEmail()));
		message.setSubject("Upgrade μλ΄");
		message.setText("μ¬μ©μλμ λ±κΈμ΄ "+ user.getLevel().name() + "λ‘ μκ·Έλ λ λμμ΅λλ€.");

		Transport.send(message);
	} catch (AddressException e) {
		throw new RuntimeException(e);
	} catch (MessagingException e) {
		throw new RuntimeException(e);
	} catch (UnsupportedEncodingException e) {
		throw new RuntimeException(e);
	}
}
```
μμ μ½λλ SMTP νλ‘ν μ½μ μ§μνλ λ©μΌ μ μ‘ μλ²κ° μ€λΉλμ΄ μλ€λ©΄, μ μμ μΌλ‘ λμν  κ²μ΄λ€. νμ§λ§, κ°λ°μλ²μΌ κ²½μ°μ SMTP νλ‘ν μ½μ μ§μνλ λ©μΌ μ μ‘μλ²κ° μ€λΉλμ΄μμ§ μμμλ μλ€. λ°λΌμ νμ€νΈκ° μ΄λ ΅λ€. 

λ§μ½ λλ€κ³  νλλΌλ νμ€νΈμλ§λ€ λ©μΌμ΄ μ μ‘μ΄ λλκ² μ³μκΉ..? μ€μ  λ©μΌ μλ²λ₯Ό μ¬μ©νμ§ μκ³  νμ€νΈ λ©μΌ μλ²λ₯Ό μ΄μ©νμ¬ νμ€νΈ μ±κ³΅μ¬λΆλ₯Ό νλ¨νλκ² κ°μ₯ μ ν©ν λ°©λ²μΈ κ² κ°λ€. μ¦, μΈλΆλ‘ μ§μ  λ©μΌμ λ°μ‘νμ§λ μμ§λ§, JavaMail κ³Ό μ°λν΄μ λ©μΌ μ μ‘ μμ²­μ λ°λ κ²κΉμ§λ§ λ΄λΉνλ κ²μ΄λ€. 

### **μ, μ΄μ  κ°λ°μ€ λλ νμ€νΈλ₯Ό μν μΆμνλ₯Ό ν΄λ³΄μ.**

```java
// μ€νλ§μ΄ μ κ³΅νλ JavaMailμ μλΉμ€ μΆμν μΈν°νμ΄μ€
public interface MailSender {
	void send(SimpleMailMessage simpleMessage) throws MailException;
	void send(SimpleMailMessage[] simpleMessage) throws MailException;
}
```
μ΄λ₯Ό μ΄μ©ν΄μ λ©μΌλ°μ‘ λ©μλλ₯Ό κ΅¬νν΄λ³΄μ. μ€νλ§μ DIλ₯Ό μ΄μ©ν΄μ `MailSender`λ₯Ό μ£Όμλ°μλ€. 
```java
... 
public class UserService {
	...
	private MailSender mailSender;

	public void setMailSender(MailSender mailSender){
		this.mailSender = mailSender;
	}

	private coid sendUpgradeEMail (User user) {
		SimplaMailMessage mailMessage = new SimplaMailMessage();
		mailMessage.setTo(user.getEmail());
		mailMessage.setFrom("useradmin@ksug.org");
		mailMessage.setSubject("Upgrade μλ΄");
		mailMessage.setText("μ¬μ©μλμ λ±κΈμ΄ "+ user.getLevel().name() + "λ‘ μκ·Έλ λ λμμ΅λλ€.");

		this.mailSender.send(mailMessage);
	}
}
```
μ€νλ§μμ μ κ³΅ν΄μ£Όλ MailSenderλ₯Ό κ΅¬νν ν΄λμ€λ `JavaMailSenderImpl` μ΄λΌλ ν΄λμ€μ΄λ€. μ°λ¦¬λ μ΄λ₯Ό μ¬μ©νμ§ λ§κ³  νμ€νΈμ©μΌλ‘ μλ¬΄κ²λ νμ§ μλ MailSender μΈν°νμ΄μ€λ₯Ό κ΅¬νν λΉ ν΄λμ€λ₯Ό νλ λ§λ€μ. (κ΅¬νμ νμ§ μλ μ΄μ λ λ©μΌμ μ€μ λ‘ λ³΄λΌ νμκ° μκΈ° λλ¬Έμ! νμ€νΈλκΉ!) κ·Έλ¦¬κ³  λΉμ λ±λ‘ν  λ `JavaMailSenderImpl`κ° μλ `DummyMailSender`λ‘ λ±λ‘ν΄μ£Όλ κ²μ μμ§λ§μ. 
```java
public class DummyMailSender implements MailSender {
	public void send(SimpleMailMessage mailMessage) throws MailException {
	}

	public void send(SimpleMailMessage[] mailMessage) throws MailException {
	}
}
```
μ΄λ κ² μ½λλ₯Ό μμ±νλ€λ©΄ νμ€νΈ μ½λλ λ€μκ³Ό κ°μ΄ μμ±ν  μ μλ€. 
```java
// Test μ½λ
public class UserServiceTest {
	@Autowired
	MailSender mailSender;

	@Test
	public void upgradeAllOrNothing() throws Exception {
		...
		testUserService.setMailsender(mailSender);
	}
}
```
![image](https://user-images.githubusercontent.com/63777714/146325187-3ff16a83-a8ae-4b1f-8a0b-46c6113db44b.png)

μ΄μ²λΌ μ€νλ§μ΄ μ§μ  μ κ³΅ν΄μ£Όλ μΆμν ν΄λμ€λ `JavaMailServiceImpl` μ΄μ§λ§, κ·Έ μμκ³μΈ΅μΈ `MailSender`λ₯Ό μ΄μ©νμ¬ κ΅¬ν ν΄λμ€λ₯Ό λ§λ€μ΄ `MailSender`λ₯Ό DIλ₯Ό ν΅ν΄ μ¬μ©νλ©΄ λ§€μ° μ μ©νκ² μ¬μ©ν  μ μλ€. 

---
## **νμ€νΈ λμ­μ μ’λ₯μ νΉμ§**
μμ μμμ λ΄€λ―μ΄ νμ€νΈ λμμ΄ λλ μ€λΈμ νΈκ° λ λ€λ₯Έ μ€λΈμ νΈμ μμ‘΄νλ κ²½μ°λ λΉμΌλΉμ¬νλ€. (μλ₯Ό λ€λ©΄, UserServiceλ DIλ°λ κ°μ²΄κ° `UserDao`,`MailSender`,`PlatformTransactionManager`μΈ κ°λ λ¨) νμ§λ§, MailSenderκ°μ κ²½μ° μ€μ λ‘ λ°μ‘μ΄ λλ©΄ μλκΈ° λλ¬Έμ MailSenderλ₯Ό κ΅¬νν μλ¬΄μΌλ νμ§ μλ DummyMailSenderλ₯Ό μ΄μ©νμ¬ νμ€νΈ λμ­μΌλ‘ μ¬μ©νλ€. 

> π‘ μ΄λ κ² νλμ μ€λΈμ νΈκ° μ¬μ©νλ μ€λΈμ νΈλ€μ DIμμ μμ‘΄ μ€λΈμ νΈλΌκ³  λΆλ λ€. μμ‘΄νλ€λ κ²μ μ’μλκ±°λ κΈ°λ₯μ μ¬μ©νλ€λ μλ―Έμ΄λ€. μμ κΈ°λ₯μ΄λΌν΄λ λ€λ₯Έ μ€λΈμ νΈμ κΈ°λ₯μ μ¬μ©νλ€λ©΄, μ¬μ©νλ μ€λΈμ νΈμ κΈ°λ₯μ΄ λ°λμμ λ μμ μ΄ μν₯μ λ°μ μ μκΈ° λλ¬Έμ μμ‘΄νκ³  μλ€κ³  νλ€. μμ‘΄ μ€λΈμ νΈλ₯Ό **νλ ₯ μ€λΈμ νΈ**λΌκ³ λ νλ€. ν¨κ» νλ ₯ν΄μ μΌμ μ²λ¦¬νλ λμμ΄κΈ° λλ¬Έμ΄λ€. 

μ΄μ²λΌ νμ€νΈ νκ²½μ λ§λ€μ΄μ£ΌκΈ° μν΄ νμ€νΈ λμμ΄ λλ μ€λΈμ νΈμ κΈ°λ₯μλ§ μΆ©μ€νκ² μννλ©΄μ νμ€νΈλ₯Ό μ€ν κ°λ₯νλλ‘ λ§λ€μ΄μ£Όλ μ€λΈμ νΈλ€μ ν΅νμ΄μ <u>**νμ€νΈ λμ­**</u>μ΄λΌκ³  λΆλ₯Έλ€. 

### **νμ€νΈ μ€ν(test stub)**
λνμ μΈ νμ€νΈ λμ­μ νμ€νΈ μ€ν(test stub)μ΄λ€. νμ€νΈ λμ μ€λΈμ νΈμ μμ‘΄κ°μ²΄λ‘μ μ‘΄μ¬νλ©΄μ νμ€νΈ λμμ μ½λκ° μ μμ μΌλ‘ μνν  μ μλλ‘ λλ κ²μ λ§νλ€. μΌλ°μ μΌλ‘ λ©μλλ₯Ό ν΅ν΄ μ λ¬λλ νλΌλ―Έν°μ λ¬λ¦¬, νμ€νΈ μ½λ λ΄λΆμμ κ°μ μ μΌλ‘ μ¬μ©λλ€. λ°λΌμ DO λ±μ ν΅ν΄ λ―Έλ¦¬ μμ‘΄ μ€λΈμ νΈλ₯Ό νμ€νΈ μ€νμΌλ‘ λ³κ²½ν΄μΌνλ€. (μ, `DummyMailSender`)

λν μ€νμ΄ κ²°κ³Όλ₯Ό λλ €μ€μΌ ν  λλ μλ€. μμν κ²°κ³Ό λλ μμν μμΈμν©μ λν νμ€νΈλ₯Ό ν  λμλ μ μ©μ΄ κ°λ₯νλ€. (μνκ²μ¦..?)

### **λͺ© μ€λΈμ νΈ(mock object)**
νμ€νΈ μ€νμΌλ‘ νμ μμ²΄μ λν΄μ assertThat()μΌλ‘ κ²μ¦νλ κ²μ λΆκ°λ₯νλ€. κ·Έλ΄ λ **λͺ© μ€λΈμ νΈ**λ₯Ό μ¬μ©νμ¬ νμ€νΈ λμ μ€λΈμ νΈμ μμ‘΄ μ€λΈμ νΈ μ¬μ΄μμ μΌμ΄λλ μΌμ κ²μ¦ν  μ μλλ‘ ν΄μΌνλ€. 

<u>λͺ© μ€λΈμ νΈλ μ€νμ²λΌ μ€λΈμ νΈκ° μ μμ μΌλ‘ μ€νλλλ‘ λμμ£Όλ©΄μ νμ€νΈ μ€λΈμ νΈμ μμ μ μ¬μ΄μμ μΌμ΄λλ μ»€λ?€λμΌμ΄μ λ΄μ©μ μ μ₯ν΄λλ€κ° νμ€νΈ κ²°κ³Όλ₯Ό κ²μ¦νλ λ° νμ©ν  μ μκ² ν΄μ€λ€. (νμκ²μ¦) </u>

> π **μλ₯Ό λ€μ΄λ³΄μ!**<br>
> UserServiceμμ νΈλμ­μμ νμ€νΈ νλ μ½λ!! κ²°κ³Όλ§ μ€μνλ€. μ¦, μ μ²΄κ° μλ°μ΄νΈ λμλμ§, μλμ§ κ²°κ³Ό! μνκ°λ§ μ€μνλ€!! --> κ·Έλ¬λ **μνκ²μ¦**! **νμ€νΈ μ€ν**μ μ¬μ©ν΄μ κ²μ¦ν  μ μλ€. <br>
> 
> UserService μμ λ©μΌ λ°μ‘μ νμ€νΈ νλ μ½λ!! λ©μΌμ΄ μ λ§ MailSenderλ₯Ό ν΅ν΄μ λ°μ‘μ΄ μ μ΄λ£¨μ΄ μ‘λμ§! νμμ λν κ²μ¦μ΄ νμνλ€! λ©μΌμ΄ λ°μ‘μ΄ λμ΄λ κ²°κ³Όκ°μ μκΈ° λλ¬Έμ κ²°κ³Όλ‘λ κ²μ¦μ΄ μ΄λ ΅λ€.(μ€νλ§μΌλ‘ κ²μ¦ μ΄λ €μ) --> **λͺ© μ€λΈμ νΈ**λ₯Ό ν΅ν΄ νμκ° μ μ΄λ£¨μ΄ μ‘λμ§ κ²μ¦μ΄ νμν¨ **νμκ²μ¦**!



--- 
## **μ λ¦¬**
* λΉμ¦λμ€ λ‘μ§μ λ΄μ μ½λλ λ°μ΄ν°μμΈμ€ λ‘μ§μ λ΄μ μ½λμ κΉλνκ² λΆλ¦¬λλ κ²μ΄ λ°λμ§νλ€. λΉμ€λμ€ λ‘μ§ μ½λ λν λ΄λΆμ μΌλ‘ μ±μκ³Ό μ­ν μ λ°λΌμ κΉλνκ² λ©μλλ‘ μ λ¦¬λμ΄μΌ νλ€. 
* μ΄λ₯Ό μν΄μλ DAOμ κΈ°μ  λ³νμ μλΉμ€ κ³μΈ΅μ μ½λκ° μν₯μ λ°μ§ μλλ‘ μΈν°νμ΄μ€μ DIλ₯Ό μ νμ©ν΄μ κ²°ν©λλ₯Ό λ?μΆ°μ€μΌ νλ.
* DAOλ₯Ό μ¬μ©νλ λΉμ¦λμ€ λ‘μ§μλ λ¨μ μμμ λ³΄μ₯ν΄μ£Όλ νΈλμ­μμ΄ νμνλ€. 
* νΈλμ­μμ μμκ³Ό μ’λ£λ₯Ό μ§μ νλ μΌμ νΈλμ­μ κ²½κ³ μ€μ μ΄λΌκ³  νλ€. νΈλμ­μ κ²½κ³μ€μ μ μ£Όλ‘ λΉμ¦λμ€ λ‘μ§ μμμ μΌμ΄λλ κ²½μ°κ° λ§λ€. 
* μμλ νΈλμ­μ μ λ³΄λ₯Ό λ΄μ μ€λΈμ νΈλ₯Ό νλΌλ―Έν°λ‘ DAOμ μ λ¬νλ λ°©λ²μ λ§€μ° λΉν¨μ¨μ μ΄κΈ° λλ¬Έμ μ€νλ§μ΄ μ κ³΅νλ νΈλμ­μ λκΈ°ν κΈ°λ²μ νμ©νλ κ²μ΄ νΈλ¦¬νλ€. 
* μλ°μμ μ¬μ©λλ νΈλμ­μ APIμ μ’λ₯μ λ°©λ²μ λ€μνλ€. νκ²½κ³Ό μλ²μ λ°λΌμ νΈλμ­μ λ°©λ²μ΄ λ³κ²½λλ©΄ κ²½κ³μ€μ  μ½λλ ν¨κ» λ³κ²½λμ΄μΌ νλ€. 
* νΈλμ­μ λ°©λ²μ λ°λΌ λΉμ¦λμ€ λ‘μ§μ λ΄μ μ½λκ° ν¨κ» λ³κ²½λλ©΄ λ¨μΌ μ±μ μμΉμ μλ°°λλ©°, DAO κ° μ¬μ©νλ νΉμ  κΈ°μ μ λν΄ κ°ν κ²°ν©μ λ§λ€μ΄λΈλ€. 
* νΈλμ­μ κ²½κ³μ€μ  μ½λκ° λΉμ¦λμ€ λ‘μ§ μ½λμ μν₯μ μ£Όμ§ μκ² νλ €λ©΄ μ€νλ§μ΄ μ κ³΅νλ νΈλμ­μ μλΉμ€ μΆμνλ₯Ό μ΄μ©νλ©΄ λλ€. 
* μλΉμ€ μΆμνλ λ‘μ° λ λ²¨μ νΈλμ­μ κΈ°μ κ³Ό APIμ λ³νμ μκ΄μμ΄ μΌκ΄λ APIλ₯Ό κ°μ§ μΆμν κ³μΈ΅μ λμνλ€. 
* μλΉμ€ μΆμνλ νμ€νΈνκΈ° μ΄λ €μ΄ JavaMail κ°μ κΈ°μ μλ μ μ©ν  μ μλ€. νμ€νΈλ₯Ό νΈλ¦¬νκ² μμ±νλλ‘ λμμ£Όλ κ²λ§μΌλ‘λ μλΉμ€ μΆμνλ κ°μΉκ° μλ€. 
* νμ€νΈ λμμ΄ μ¬μ©νλ μμ‘΄ μ€λΈμ νΈλ₯Ό λμ²΄ν  μ μλλ‘ λ§λ  μ€λΈμ νΈλ₯Ό νμ€νΈ λμ­μ΄λΌκ³  νλ€. 
* νμ€νΈ λμ­μ νμ€νΈ λμ μ€λΈμ νΈκ° μννκ² λμν  μ μλλ‘ λμ°λ©°μ νμ€νΈλ₯Ό μν΄ κ°μ μ μΈ μ λ³΄λ₯Ό μ κ³΅ν΄μ£ΌκΈ°λ νλ€.
* νμ€νΈ λμ­ μ€μμ νμ€νΈ λμμΌλ‘λΆν° μ λ¬λ°μ μ λ³΄λ₯Ό κ²μ¦ν  μ μλλ‘ μ€κ³λ κ²μ λͺ© μ€λΈμ νΈλΌκ³  νλ€. 













