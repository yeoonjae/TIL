# **네트워크**
## 📖 **HTTP란?**
**H**yper**T**ext **T**ransfer **P**rotocol의 약자로 HyperText 문서를 교환하기 위해 사용되는 통신 규약이다. 

### 🔍 **HTTP 의 특징**
* 클라이언트 - 서버 구조
* 무상태 프로토콜(stateless), 비연결성
* HTTP 메세지로 통신
* 단순하고 확장 가능

---
## 📖 **TCP 와 UDP**
**TCP의 특징**
* 연결지향 - TCP 3 way handshake(가상 연결)
* 데이터 전달 보증
* 순서 보장 
* 신뢰할 수 있는 프로토콜로 현재 많이 사용됨

**UDP의 특징**
* 데이터 전달 및 순서가 보증되지 않음
* 빠른 전송 가능 (3way ahndshake를 하지 않음)
* 일방적인 전송 시에 사용함

**TCP와 UDP의 차이점**
* TCP는 신뢰성이 있는 연결을, UDP는 빠른 전송을 지향하는 프로토콜이다. 
* TCP는 데이터를 완벽히 보내는 것(전달받았는지 확인까지)을 목표로 하고, UDP는 일방적인 데이터 전송을 목표로 한다. 

---
## 📖 **3 way handshake**
![image](https://user-images.githubusercontent.com/63777714/148722712-2eb34713-e440-401c-b8c8-a15386a54d16.png)


---
## 📖 **Port**
* 네트워크를 통해 데이터를 주고받는 프로세스를 식별하기 위해 호스트 내부적으로 프로세스가 할당받는 고유한 값이다. 
* ex) IP가 하나의 아파트를 나타낸다면 port는 동, 호수를 나타낸다. 

다음사진은 포트 포워딩 과정을 나타낸 사진이다. 

![image](https://user-images.githubusercontent.com/63777714/148724146-8fcf6909-40f1-49d1-8cf2-65e2f64c52f0.png)


---
## 📖 **서버로 요청하면 일어나는 일련의 과정**

1. 브라우저에 URL 입력
2. 호스트 명 획득
3. DNS 서버에 IP주소 요청
4. 포트(Port) 번호 획득
5. HTTP 메세지 생성 및 IP 주소로 포트 번호에 연결(Connection)
    * 이 때, 3-Hand-Shaking이 일어남
6. 클라이언트가 HTTP 메서드를 통하여 서버에 요청
7. 서버가 HTTP 상태 코드로 클라이언트에 응답
8. 커넥션 종료

좀 더 자세히 알아보자.

1. 기본 정보를 확인합니다.
    * 브라우저의 로컬 hosts파일에 입력한 URL에 연결된 정보가 있는지 확인
2. 외부와 통신할 준비
    * DHCP(Dynamic Host Configuration Protocol)에서 브라우저의 IP 주소, 가장 가까운 라우터(Router)의 IP 주소, 가장 가까운 DNS 서버의 IP 주소를 수신
    * 주소 결정 프로토콜(Address Resolution Protocol, ARP)로 이동하려는 URL 호스트에 연결된 IP 주소와 가장 가까운 라우터의 MAC 주소를 확인
3. DNS 서버와 IP 주소를 송수신
    * DNS Query를 DNS 서버에 송신하면 DNS 서버는 웹 서버의 IP 주소를 브라우저로 반환
4. 웹 서버에 접속(연결)
    * 요청을 위해 TCP 소켓을 개방한뒤 연결(Connection). 이 과정에서 3-Hand-Shaking이 일어나며, TCP 연결이 성공되면 요청이 소켓을 통해 전송
5. 서버는 요청을 처리하 클라이언트에게 응답하며, 커넥션을 종료
6. 응답된 전송을 브라우저가 수신



---
참고 <br>
https://bmind305.tistory.com/25