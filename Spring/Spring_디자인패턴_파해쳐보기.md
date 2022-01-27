
## 1. 🔍 **프록시 패턴**

프록시 패턴은 어떤 객체에 대한 **접근을 제어**하기 위한 목적으로 쓰이며, 실제 객체 메소드를 호출하면 중간에서 호출을 가로채는 패턴을 말합니다. 

### Spring AOP(Aspect Oriented Programming)

스프링에서 프록시 패턴이 적용된 부분입니다. AOP 는 관점 지향 프로그래밍으로 핵심 기능과 부가 기능을 분리시켜 하나의 관점으로 만들어 이를 재사용하는 방식입니다. 

---

## 2. 🔍 **싱글턴 패턴**

애플리케이션이 실행할 때 **최초 한번만** 메모리를 할당하고 그 메모리에 인스턴스를 만들어 사용하는 디자인 패턴입니다. 커넥션 풀, 스레드 풀, 디바이스 설정 객체 등과 같은 경우 인스턴스를 여러 개 만들게 되면 불필요한 자원을 사용하게 되고 이런 연결은 비용이 많이 들기 때문에 인스턴스 하나만 만들고 그것을 계속해서 재사용합니다.

즉, **실제로 생성되는 객체는 하나**이고, 최초 생성 이후에 호출된 생성자는 생성한 객체를 반환합니다.

스프링에서는 애플리케이션이 실행할 때 스프링 컨테이너가 관리하는 빈을 생성하고 한 번 생성된 빈은 최초 한 번만 메모리를 할당하고 스프링 컨테이너 안에서 등록된 이 빈을 호출할 때마다 같은 인스턴스를 반환합니다. 

---

## 3. 🔍 **템플릿 메소드 패턴**

템플릿 메소드 패턴은 이름 그대로 **템플릿을 사용하는 패턴**입니다. 템플릿은 기준이 되는 거대한 틀로 변하지 않는 부분을 템플릿이라는 틀에 모아둡니다. 그리고 일부 변하는 부분을 별도로 호출하여 해결합니다.

### **DispatcherServlet**

![image](https://user-images.githubusercontent.com/63777714/151388158-aa8f818d-fa0b-469e-b164-ce10457d5193.png)

![image](https://user-images.githubusercontent.com/63777714/151388250-b06c4709-5f12-4e2b-be09-f0c796d380f2.png)

![image](https://user-images.githubusercontent.com/63777714/151388340-464e9fc8-d75d-4157-9b8a-46f7e8f8e508.png)


```java
public abstract class FrameworkServlet extends HttpServletBean implements ApplicationContextAware {

	...생략

	protected final void processRequest(HttpServletRequest request, HttpServletResponse response)
				throws ServletException, IOException {
	
			... 생략

			try {
				doService(request, response); // doService 호출
			}
			catch (ServletException | IOException ex) {
				failureCause = ex;
				throw ex;
			}
			... 생략
	}

	...
	// doService는 추상 메소드 --> 하위 클래스에게 구현을 위임 (DispatcherServlet 에게 위임)
	protected abstract void doService(HttpServletRequest request, HttpServletResponse response)
				throws Exception;
}
```

스프링에서는 `DispatcherServlet`에서 템플릿 메소드 패턴이 사용되고 있습니다. 

`DispatcherServlet`의 `doService()` 메소드는 http 요청에 대해 처리하는 메소드입니다. `DispatcherServlet`은 `FrameworkServlet`의 상속을 받은 클래스입니다. `FrameworkServlet`의 `processRequest()` 메소드 안에선 `doService()`를 템플릿 메소드 패턴을 이용해 호출하게 되는데 이 `doService()`는 추상 메소드로 정의되어 있어 하위 클래스인 `DisparcherServlet`에서 오버라이딩 된 `doService()`를 호출하게 됩니다.

즉, `FrameworkServlet`이 커다란 틀인 템플릿이 되는 것이고, 변경되는 부분을 추상 메소드로 정의함으로써 하위 클래스인 `DisparcherServlet`에서 구현하여 사용하는 방식을 적용하고 있습니다. 

### JDBCTemplate

먼저 JDBC(**Java DataBase Connectivity** : **Java에서 데이터 베이스에 접속할 수 있도록 해주는 Java API**)를 통해 DB 연결을 할 때의 순서는 다음과 같습니다.

1. 드라이버 로딩
2. 데이터 베이스를 연결
3. 쿼리 실행
4. 커넥션 해제

```java
public class DBConnection {	// DB 연결 클래스 
	public static Connection getConnection() throws ClassNotFoundException {
		**// 드라이버 로딩** 
		Class.forName("com.mysql.cj.jdbc.Driver");	

		Connection conn = null;
		try {
			//자신의 DB 정보에 맞는 user와 pw 설정 
			String user = "user"; 	
			String pw = "pw";
			String url = "jdbc:mysql://localhost:3306/project?serverTimezone=Asia/Seoul&characterEncoding=utf8";
			**// DB 연결** 
			conn = DriverManager.getConnection(url, user, pw);
		} catch (SQLException sqle) {
			System.out.println("DB error : " + sqle.toString());
		} catch (Exception e) {
			System.out.println("Unkonwn error");
		}
		return conn;
	}
}
```

---

## 4. 🔍 **팩토리 메소드 패턴(Factory Method Pattern)**

팩토리 메소드 패턴이란 **객체 생성 처리를 서브 클래스로 분리하여 처리하도록 캡슐화 패턴**이다.즉, 객체의 생성 코드를 별도의 클래스/메서드로 분리함으로써 객체 생성의 변화에 대비하여 유용하게 사용할 수 있습니다.

**팩토리 패턴의 장점**

- 비슷한 성격의 객체를 인터페이스를 통해 하나로 관리할 수 있다.
- 협업시 공통코드를 건드리는 일이 없이 업무를 진행할 수 있다.
- 추수 비슷한 유형의 객체가 생성되어도 implement를 통해 쉽게 추가할 수 있다.

**팩토리 메소드 패턴 사용 방법**

1. 요구사항을 담을 interface 정의

2. interface를 상속받아 구현하는 Class 정의

3. 파라미터에 따라 구현한 Class 객체를 반환해주는 Factory Class 정의

4. Factory Class를 통해 객체를 받아 사용

## 5. **전략 패턴 (**Strategy Pattern**)**

템플릿 메소드 패턴과 유사하지만 상속이 아닌 인터페이스로 정의함으로써 상속의 단점을 보완한 패턴입니다. 전략을 생성해 전략을 실행할 Context(문맥)에 주입합니다.

템플릿 콜백 패턴은 전략 패턴의 전략에 해당하는 부분을 익명 내부 클래스 또는 람다로 구현하여 인자로 넘겨주는 패턴을 말합니다. 스프링에서만 불리는 패턴으로 스프링에서 제공하는 template 은 대부분 템플릿 콜백 패턴을 사용

---

# **그 외의 패턴**

## 1. **프론트 컨트롤러(Front Controller)**

모든 리소스(Resource)의 요청을 하나의 대표 컨트롤러(Controller)로 처리하는 패턴으로 스프링은 `DispatcherServlet`이 처음 HTTP 모든 요청을 받고, HandlerMapper를 통해 요청을 처리할 Controller를 반환받아 다시 `DispatcherServlet`이 Controller에 요청을 보낸 후 작업을 처리한 후, DispatcherServlet이 다시 그 결과를 ViewResolver에 넘겨 보여줄 화면(Html, jsp)을 반환받아 해당 View를 보여주게 된다.

즉, 스프링은 DispatcherServlet이라는 프론트 컨트롤러로 제일 앞에서 서버로 들어오는 모든 요청을 처리합니다. 모든 들어오는 요청은 하나의 서블릿(DispatcherServlet)을 거쳐 해당 서블릿 내부에서 요청 라우팅을 하여 분배하는 형태입니다.

## 2. **MVC 패턴(Model View Controller Pattern)**

Model, View, Controller를 분리한 디자인 패턴으로 Controller를 통해 요청을 받고, 업무 로직(Service), 데이터 처리(DAO)는 다른 클래스로 처리하고, 다시 결과 데이터를 Model에 담아 Controller에서 사용자에게 보여줄 화면을 반환하여 해당 jsp로 View를 보여주는 형식을 사용합니다.

이런 업무로직을 Controller나 View 페이지에서 처리하지 않고 Service 클래스를 만들어 처리하는 것은 아래 View Helper Pattern 과도 연관이 있다.

## 3. **뷰 헬퍼(View Helper)**

View에서 제어 로직, 데이터 액세스, 화면 구성 등을 모두 넣는 것이 아닌, View는 단순히 클라이언트에게 정보를 보여주는 역할, Controller는 View와 Model을 연결해주는 역할, helper는 View에서 사용할 데이터 모델을 적용하는 것을 포함하여 데이터를 가공하는 역할을 한다.

Spring에서는 보통 View(jsp), Controller(controller), Service(helper), DAO(helper)로 업무 로직과 데이터 액세스 로직을 나누어 Service, DAO 클래스를 헬퍼로 지정하는 방식을 사용한다.

---

참고

[https://blog.naver.com/PostView.nhn?blogId=hj_kim97&logNo=222295968816](https://blog.naver.com/PostView.nhn?blogId=hj_kim97&logNo=222295968816)