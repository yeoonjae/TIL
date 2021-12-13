> 📚 본 글은 토비의 스프링 3.1을 읽고 정리한 글입니다. 

<br>

# **3장 템플릿**

### 🤷‍♂️ **템플릿이란 ?** 
바뀌는 성질이 다른 코드 중에서 변경이 거의 일어나지 않으며 일정한 패턴으로 유지되는 특성을 가진 부분을 자유롭게 변경되는 성질을 가진 부분으로부터 독립시켜서 효과적으로 활용할 수 있도록 하는 방법이다. 

### **예외처리 기능을 갖춘 UserDao의 `deleteAll()` 메소드** 
```java

public class UserDao {
	private DataSource dataSource;
		
	public void setDataSource(DataSource dataSource) {
		this.dataSource = dataSource;
	}

	public void deleteAll() throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = dataSource.getConnection();
            ps = c.prepareStatement("delete from users");
		    ps.executeUpdate();
        } catch (SQLException e) {
            throw e;    // 예외가 발생했을 때 부가적인 작업을 해줄 수 있도록 catch블록을 둔다. 
        } finally {
            if (ps != null) {
                try {
                    ps.close();     // 리소스 반환
                } catch (SQLException e) {}
            }
            if (c != null) {
                try {
                    c.close();       // 리소스 반환
                } catch (SQLException e) {}
            }
        }

		ps.close();
		c.close();
	}	
}
```
---
## **템플릿 메소드 패턴을 적용**
<br>

#### 🤷‍♂️**템플릿 메소드 패턴이란?**
상속을 통해 기능을 확장해서 사용하는 부분이다. 변하지 않는 부분은 슈퍼클래스에 두고 변하는 부분을 추상 메소드로 정의해둬서 서브클래스에서 오버라이드하여 새롭게 정의해 쓰도록 하는 것이다. 

위의 UserDao의 `deleteAll()` 메소드에 템플릿 메소드 패턴을 적용해보자. 
우선 변하는 부분을 추출해서 추상메소드로 따로 만들어보자. 추상메소드를 가지게 되는 UserDao 역시 추상클래스로 변경해주자. 

```java
abstract protected PreparedStatement makeStatement (Connection c) throws SQLException;
```

그리고 이를 상속하는 서브클래스를 만들고 그 클래스에서 위의 추상메소드를 구현하자.

```java
// makeStatement()를 구현한 UserDao 서브클래스
public class UserDaoDeleteAll extends UserDao {
    protected PreparedStatement makeStatement (Connection c) throws SQLException {
        PreparedStatement ps = c.prepareStatement("delete from users");
        return ps;
    }
}
```
이와 같이 적용하면 Connection과 PrepareStatement의 리소스를 필요로 하는 모든 메소드들은 슈퍼클래스로 정의한 UserDao를 상속받게될 것이다. 그렇다면 다음과 같은 구조가 된다. 

![image](https://user-images.githubusercontent.com/63777714/145789494-b38254d2-54bb-40f5-9da6-3acb65cd06af.png)

하지만 이처럼 템플릿 메소드 패턴을 적용하면 DAO 로직마다 상속을 통해야 한다는 단점이 존재한다. JDBC 을 필요로하는 로직이 10개라면 10개의 클래스를 만들어 상속받아야한다는 것이다. 

따라서 이는 유연성이 떨어진다. 

---
## **전략 패턴을 적용**
<br>

#### 🤷‍♂️**전략 패턴이란?**
오브젝트를 아예 둘로 분리하고 클래스 레벨에서는 인터페이스를 통해서만 의존하도록 만드는 것이다. 
개방 폐쇄 원칙(OCP)을 잘 지키는 구조이면서도 템플릿 메소드 패턴보다 유연화고 확장성이 뛰어나다. OCP 관점에서 확장에 해당하는 변하는 부분을 별도의 클래스로 만들어 추상화된 인터페이스를 통해 위임하는 방식이다. 

![image](https://user-images.githubusercontent.com/63777714/145791493-33709688-dcbd-4b02-b58d-a36f503b5773.png)

위 그림에서 좌측에 있는 Context의 `contextMethod()`에서 일정한 구조를 가지고 동작하다가 특정 확장 기능은 Strategy 인터페이스를 통해 외부의 독립된 전략 클래스에 위임하는 것이다. 

UserDao 클래스에 위의 전략을 적용하면 `deleteAll()`은 JDBC를 이용하여 DB를 업데이트 하는 작업이라는 변하지 않는 맥락(context)을 갖는다. PreparedStatement를 생성하는 외부 기능이 전략이라고 볼 수 있다. 

UserDao의 전략 인터페이스를 만들기 위해선 DB 커넥션을 전달받아야하고, 전달받은 DB 커넥션을 통해 만들어진 PreparedStatement을 반환해야한다. 

```java
// statementStrategy 인터페이스
public interface StatementStrategy {
    PreparedStatement makePreparedStatement (Connection c) throws SQLException;
}
```
위 인터페이스를 상속해서 PreparedStatement를 생성하는 클래스를 만들어보자. 
```java
public class DeleteAllStatement implements StatementStrategy {
    PreparedStatement makePreparedStatement (Connection c) throws SQLException {
        PreparedStatement ps = c.prepareStatement("delete from users");
        return ps;
    }
}
```

```java
// 전략패턴을 따라 DeleteAllStatement가 적용된 delelteAll() 메소드
public class UserDao {
	private DataSource dataSource;
		
	public void setDataSource(DataSource dataSource) {
		this.dataSource = dataSource;
	}

	public void deleteAll() throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = dataSource.getConnection();

            StatementStrategy statementStrategy = new DeleteAllStatement(); // 전략패턴 적용
            ps = statementStrategy .makePreparedStatement(c)

		    ps.executeUpdate();
        } catch (SQLException e) {
            throw e;    // 예외가 발생했을 때 부가적인 작업을 해줄 수 있도록 catch블록을 둔다. 
        } finally {
            if (ps != null) {
                try {
                    ps.close();     // 리소스 반환
                } catch (SQLException e) {}
            }
            if (c != null) {
                try {
                    c.close();       // 리소스 반환
                } catch (SQLException e) {}
            }
        }

		ps.close();
		c.close();

	}	
}
```
전략패턴을 적용하긴 했지만 컨텍스트 안에서 구체적인 DeleteAllStatement를 사용하도록 고정이 되어있는 부분이 신경쓰인다! OCP에 잘 들어맞는다고 할 수 없다! 컨텍스트에 해당하는 부분을 메소드로 따로 빼고 사용할 전략을 Client(사용할 메소드)가 입력하게끔 바꿔보자. 

```java
// 메소드로 분리한 context (try/catch/finally)
public void jdbcContextWithStatementStrategy (StatementStrategy stmt) throws SQLException {
    Connection c = null;
    PreparedStatement ps = null;

    try {
        c = dataSource.getConnection();

        ps = stmt.makePreparedStatement(c); // 클라이언트에게 입력받은 전략 사용

        ps.executeUpdate();
    } catch (SQLException e) {
        throw e;    
    } finally {
        if (ps != null) {
            try {
                ps.close();     
            } catch (SQLException e) {}
        }
        if (c != null) {
            try {
                c.close();      
            } catch (SQLException e) {}
        }
    }
}
```
```java
// 변경된 deletAll() 메소드
public void deleteAll() throws SQLException {
    StatementStrategy statementStrategy = new DeleteAllStatement(); // 선정한 전략 클래스의 오브젝트 생성
    jdbcContextWithStatementStrategy(statementStrategy); // 컨텍스트 호출 전략 오브젝트 전달
}	
```
---
## **조금 더 개선해보자.**
<br>
중첩클래스를 적용해서 메소드마다 매번 클래스를 만들어야 하는 부분을 개선해보자. 

<br>


> 🕵🏻‍♂️ **중첩클래스**<br>
> 다른 클래스 내부에 정의되는 클래스를 중첨 클래스(nested class) 라고 한다. 중첩 클래스는 독립적으로 오브젝트로 만들어질 수 있는 스태틱 클래스(static class)와 자신이 정의된 클래스의 오브젝트 안에서만 만들어질 수 있는 내부 클래스(inner class)로 구분된다. 
> 
> 내부 클래스는 다시 범위(scope)에 따라 세가지로 구분된다. 멤버 필드처럼 오브젝트 레벨에 정의되는 멤버 내부 클래스(member inner class)와 메소드 레벨에 정의되는 로컬 클래스(local class), 그리고 이름을 갖지 않는 익명 내부 클래스(anonymous inner class)다. 익명 내부 클래스의 범위는 선언된 위치에 따라서 다르다. 
>
> 🕵🏻‍♂️ **익명 내부 클래스**<br>
> 익명 내부 클래스(anonymous inner class)는 이름을 갖지 않는 클래스다. 클래스 선언과 오브젝트 생성이 결합된 형태로 만들어지며 , 상속할 클래스나 구현할 인터페이스를 생성자 대신 사용해서 다음과 같은 형태로 만들어 사용한다. 클래스를 재사용할 필요가 없고, 구현한 인터페이스 타입으로만 사용할 경우에 유용하다. 
> 
> `new 인터페이스이름() { 클래스 본문 };`

```java
// 익명 내부 클래스를 적용한 deleteAll() 메소드
public void deleteAll() throws SQLException {
    jdbcContextWithStatementStrategy{
        new StatementStrategy() {
            PreparedStatement makePreparedStatement (Connection c) throws SQLException {
                return c.prepareStatement("delete from users");
            }
        }
    };
}
```
전략패턴의 관점에서 볼 때 UserDao 의 메소드가 클라이언트, 익명 내부 클래스로 만들어진 것이 개별적인 전략, `jdbcContextWithStatementStrategy()` 메소드가 컨텍스트라 볼 수 있다. 

---
## **클래스 분리**
<br>

`jdbcContextWithStatementStrategy()` 메소드를 다른 DAO 에서도 사용할 수 있도록 클래스를 분리해보자. 
```java
// JDBC 작업 흐름을 분리해서 만든 jdbcContext 클래스
public class JdbcContext {
	DataSource dataSource;
	
	public void setDataSource(DataSource dataSource) {
		this.dataSource = dataSource;
	}
	
	public void workWithStatementStrategy(StatementStrategy stmt) throws SQLException {
		Connection c = null;
		PreparedStatement ps = null;

		try {
			c = dataSource.getConnection();

			ps = stmt.makePreparedStatement(c);
		
			ps.executeUpdate();
		} catch (SQLException e) {
			throw e;
		} finally {
			if (ps != null) { try { ps.close(); } catch (SQLException e) {} }
			if (c != null) { try {c.close(); } catch (SQLException e) {} }
		}
	}
}
```
```java
// UserDao 에도 JdbcContext DI 받아서 사용하도록 변경한다. 
public class UserDao {
    ... 
    private JdbcContext jdbcContext;

    public void setJdbcContext(JdbcContext jdbContext) {
        this.jdbcContext = jdbcContext;
    }
    ...
}
```
---
## **템플릿과 콜백**
<br>

스프링에서는 전략패턴의 기본구조에 익명 내부 클래스를 활용한 방식을 **템플릿/콜백 패턴**이라고 부른다. 전략패턴의 컨텍스트를 템플릿이라 부르고, 익명 내부 클래스로 만들어지는 오브젝트를 콜백이라고 부른다. 

템플릿은 고정된 작업 흐름을 가진 코드를 재사용한다는 의미에서 붙인 이름이다. 콜백은 템플릿 안에서 호출되는 것을 목적으로 만들어진 오브젝트를 말한다. 


> 🕵🏻‍♂️ **템플릿** <br>
> 템플릿(Template)은 어떤 목적을 위해 미리 만들어둔 모양이 있는 틀을 가리킨다. 프로그래밍에서는 고정된 틀 안에서 바꿀 수 있는 부분을 넣어서 사용하는 경우에 템플릿이라고 부른다. JSP는 HTML이라는 고정된 부분과 EL과 스크립릿이라는 변하는 부분을 넣은 일종의 템플릿 파일이다. 템프릿 메소드 패턴은 고정된 틀의 로직을 가진 템플릿 메소드를 슈퍼 클래스에 두고, 바뀌는 부분을 서브클래스의 메소드에 두는 구조로 이루어진다.
>
> 🕵🏻‍♂️ **콜백** <br>
> 콜백(callback)은 실행되는 것을 목적으로 다른 오브젝트의 메소드에 전달되는 오브젝트를 말한다. 파라미터로 전달되지만 값을 참조하기 위한 것이 아니라 특정 로직을 담은 메소드를 실행시키기 위해 사용한다. 자바에선 메소드 자체를 파라미터로 전달할 방법이 없기 때문에 메소드가 담긴 오브젝트를 전달해야 한다. 그래서 펑셔널 오브젝트(functional object)라고도 한다. 

**템플릿/콜백의 특징**

템플릿은 고정된 작업 흐름을 가진 코드를 재사용한다는 의미에서 붙인 이름이다. 콜백은 템플릿 안에서 호출되는 것을 목적으로 만들어진 오브젝트를 말한다. 

* 콜백은 일반적으로 하나의 메소드를 가진 인터페이스를 구현한 익명 내부 클래스로 만들어진다.
* 콜백 인터페이스의 메소드는 보통 파라미터가 존재하며, 이는 템플릿의 작업 흐름 중에 만들어지는 컨텍스트 정보를 전달받을 때 사용된다. 
* 콜백 오브젝트가 내부 클래스로서 자신을 생성한 클라이언트 메소드 내의 정보를 직접 참조한다는 고유 특징을 가지고 있다.

![image](https://user-images.githubusercontent.com/63777714/145816521-d70f4356-fa62-4a0a-b339-68da208efba0.png)
위 사진은 템플릿/콜백 패턴의 일반적인 작업 흐름이다.
* <u>클라이언트</u>의 역할은 템플릿 안에서 실행될 로직을 담을 오브젝트를 만들고 콜백이 참조할 정보를 제공하는 것이다. 만들어진 콜백은 클라이언트가 템플릿의 메소드를 호출할 때 파라미터로 전달된다. 
* <u>템플릿</u>은 정해진 작업 흐름을 따라 작업을 진행하다가 내부에서 생성한 참조정보를 가지고 콜백 오브젝트의 메소드를 호출한다. 
* <u>콜백</u>은 클라이언트 메소드에 있는 정보와 템플릿이 제공한 참조정보를 이용해서 작업을 수행하고 그 결과를 다시 템플릿에 돌려준다. 
* 템플릿은 콜백이 돌려준 정보를 사용해서 작업을 마저 수행한다. 경우에 따라 최종 결과를 클라이언트에 다시 돌려주기도 한다. 

---
## **스프링이 제공해주는 템플릿/콜백 `JdbcTemplate`**
<br>
위에서 만들었던 예제를 스프링이 제공해주는 템플릿/콜백 템플릿인 JdbcTemplate으로 변경해보자. 

```java
public class UserDao {
	private DataSource dataSource;
		
	public void setDataSource(DataSource dataSource) {
		this.jdbcTemplate = new JdbcTemplate(dataSource); // jdbcTemplate으로 적용
			
		this.dataSource = dataSource;
	}
	
	private JdbcTemplate jdbcTemplate;  // jdbcTemplate으로 적용
	
	...

	public void deleteAll() throws SQLException {
		this.jdbcTemplate.update("delete from users");
	}
    ...
}

```
이전 예제에서 만들었던 deleteAll() 메소드가 사용하는 콜백은 `StatementStrategy` 인터페이스의 `makePreparedStatement()` 메소드였다. 스프링에서 이에 대응되는 JdbcTemplate 의 콜백은 `PreparedStatementCreator` 인터페이스의 `createPreparedStatement()` 메소드이다. 

메소드로부터 Connection을 제공받아 PreparedStatement을 돌려준다는 면에서 구조가 동일하다. 

`PreparedStatementCreator` 타입의 콜백을 받아서 사용하는 JdbcTemplate의 템플릿 메소드는 `update()`이다. 

이 외에도 JdbcTemplate은 다양한 기능을 제공한다 JdbcTemplate을 적용한 UserDao의 전체 코드는 다음과 같다. 
```java
public class UserDao {
	public void setDataSource(DataSource dataSource) {
		this.jdbcTemplate = new JdbcTemplate(dataSource);
	}
	
	private JdbcTemplate jdbcTemplate;
	
    // RowMapper 콜백은 자주 쓰일 수 있으나 반복된다. 그러므로 하나만 생성하여 공유한다. 
	private RowMapper<User> userMapper = 
		new RowMapper<User>() {
				public User mapRow(ResultSet rs, int rowNum) throws SQLException {
				User user = new User();
				user.setId(rs.getString("id"));
				user.setName(rs.getString("name"));
				user.setPassword(rs.getString("password"));
				return user;
			}
		};

	
	public void add(final User user) {
		this.jdbcTemplate.update("insert into users(id, name, password) values(?,?,?)",
						user.getId(), user.getName(), user.getPassword());
	}

	public User get(String id) {
		return this.jdbcTemplate.queryForObject("select * from users where id = ?",
				new Object[] {id}, this.userMapper); // 두번째 인자로 받은 id는 SQL에 바인딩할 파라미터 값, 가변인자 대신 배열을 사용한다. 마지막 인자는 ResultSet 한 로우의 결과를 오브젝트에 매핑해주는 RowMapper 콜백이다. 
	} 

	public void deleteAll() {
		this.jdbcTemplate.update("delete from users");
	}

	public int getCount() {
		return this.jdbcTemplate.queryForInt("select count(*) from users");
	}

	public List<User> getAll() {
		return this.jdbcTemplate.query("select * from users order by id",this.userMapper);
	}

}

```

* UserDao 에는 User 정보를 DB에 넣거나 가져오거나 조작하는 방법에 대한 핵심적인 로직이 다 담겨있다. 
* 사용할 테이블과 필드정보가 바뀌면 UserDao의 거의 모든 코드가 함께 바뀐다. --> <u>**응집도가 높다**</u>
* JDBC API를 사용하는 방식, 예외처리, 리소스의 반납, DB 연결을 어떻게 가져올지에 대한 책임 및 관심은 JdbcTemplate에게 있다. --> 변경이 일어난다 하더라도 UserDao는 영향을 받지 않는다. --> <u>**낮은 결합도를 가지고 있다.**</u>
* 다만, JdbcTemplate이라는 템플릿 클래스를 직접 이용한다는 면에서 특정 템플릿/콜백 구현에 대한 강한 결합을 갖고있다. 

---
## **정리**
<br>

* JDBC와 같은 예외가 발생할 가능성이 있으며 공유 리소스의 반환이 필요한 코드는 반드시 try/catch/finally 블록으로 관리해야한다. 
* 일정한 흐림이 반복되면서 그 중 이룹만 바뀌는 코드가 존재한다면 전략 패턴을 적용한다. 바뀌지않는 부분은 컨텍스트로, 바뀌는 부분은 전략으로 만들고 인터페이스를 통해 유연하게 전략을 변경할 수 있도록 구성한다. 
* 같은 애플리케이션 안에서 여러 가지 종류의 전략을 다이나믹하게 구성하고 사용해야 한다면 컨텍스트를 이용하는 클라이언트 메소드에서 직접 적략을 정의하고 제공하게 만든다. (책임을 클라이언트에게 넘김)
* 클라이언트 메소드 안에 익명 내부 클래스를 사용해서 전략 오브젝트를 구현하면 코드도 간결해지고 메소드의 정보를 직접 사용할 수 있어서 편리하다. 
* 컨텍스트는 별도의 빈으로 등록해서 DI를 받거나 클라이언트 클래스에서 직접 생성해서 사용한다. 클래스 내부에서 컨텍스트를 사용할 때 컨텍스트가 의존하는 외부의 오브젝트가 있다면 코드를 이용해서 직접 DI해줄 수 있다. 
* 단일 전략 메소드를 갖는 전략 패턴이면서 익명 내부 클래스를 사용해서 매번 전략을 새로 만들어 사용하고 , 컨텍스트 호출과 동시에 전략 DI를 수행하는 방식을 <u>템플릿/콜백 패턴</u>이라고 한다. 
* 콜백의 코드에도 일정한 패턴이 반복된다면 콜백을 템플릿에 넣고 재활용하는 것이 편리하다. 
* 템플릿과 콜백의 타입이 다양하게 바뀔 수 있다면 제네릭스<T>를 이용한다. 
* 스프링은 JDBC 코드 작성을 위해 JdbcTemplate 을 기반으로 하는 다양한 템플릭과 콜백을 제공한다. 
* 템플릿은 한 번에 하나 이상의 콜백을 사용할 수도 있고, 하나의 콜백을 여러 번 호출할 수도 있다. 
* 템플릿/콜백을 설계할 때는 템플릿과 콜백 사이에 주고받는 정보에 관심을 둬야 한다. 

> 👌🏻 결론 <br> 스프링이 제공하는 템플릿/콜백을 잘 사용하려면 템플릿/콜백에 대한 이해는 물론 직접 템플릿/콜백을 만들어 활용할 수도 있어야 한다. 즉, 관심사를 분리하고, 중복된 코드를 메소드로 혹은 클래스,인터페이스로 분리하여 응집도는 높게, 결합도는 낮게 코드를 작성할 줄 알아야 한다.








