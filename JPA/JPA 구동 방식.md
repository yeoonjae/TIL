> 📚 본 글은 인프런 강의 'JPA 프로그래밍 기본편'을 듣고 정리한 글입니다. 

<br>

# **JPA 구동원리(작성중)**

JPA는 `Persistence`라는 클래스에서 `persistence.xml(설정정보)` 파일을 읽어 `EntityManagerFactory` 라는 클래스를 생성하고, 필요할 때마다 EntityManager 를 만들어 돌리는 방식으로 동작한다. 

![image](https://user-images.githubusercontent.com/63777714/167355638-3dfc600d-45d8-4a2c-bc2f-ae3b352b67bc.png)

Application 로딩시점에 EntityManagerFactory는 하나만 만든다. 트랜잭션 즉, 하나의 일련의 활동을 할 때마다 EntityManager를 만들어야한다.