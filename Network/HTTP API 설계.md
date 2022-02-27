# **HTTP API 설계** 
## **회원 관리 시스템의 API 설계 예시 - POST 기반 등록**
* **회원** 목록 --> `/members` `GET`
* **회원** 등록 --> `/members` `POST`
* **회원** 조회 --> `/members/{id}` `GET`
* **회원** 수정 --> `/members/{id}` `PATCH,PUT,POST`
* **회원** 삭제 --> `/members/{id}` `DELETE`

## **파일 관리 시스템의 API 설계 예시 - PUT 기반 등록**
* **파일** 목록 --> `/files` `GET`
* **파일** 조회 --> `/files/{filename}` `GET`
* **파일** 등록 --> `/files/{filename}` `PUT`
* **파일** 삭제 --> `/files/{filename}` `DELETE`
* **파일** 대량 등록 --> `/files` `POST`

> 💡 **PATCH와 PUT의 차이** <br>
> PATCH는 부분을 수정할 때 부분의 정보면 body에 담아서 보내도 해당 부분만 수정이 이루어집니다. 반면 PUT은 전체 수정이 됩니다. 즉, PUT은 A,B,C라는 세개의 정보 중 A라는 정보만 수정하고 싶어서 A의 정보만 입력 후 body 에 담아 보낼경우 A='입력정보',B=null,C=null 로 전송되어 원하지 않는 수정까지 이루어질 수 있습니다. 따라서 수정을 진행할 때 PATCH를 일반적으로 많이 사용하며 PUT 은 게시글과 같은 전체수정이 이루어질 때 사용합니다. 

## **HTML FORM 사용**
HTML FORM 은 GET, POST 만 사용이 가능합니다. 따라서 위에서 설계했던 PUT, PATCH , DELETE 를 사용하지 못합니다. 물론, Ajax와 같은 기술을 이용하여 해결이 가능하긴 하지만 HTML FORM 만 사용했을 때라고 가정하고 어떻게 설계해야하는지 알아보겠습니다. 

* **회원** 목록 --> `/members` `GET`
* **회원** 등록 폼 --> `/members/new` `GET`
* **회원** 등록 --> `/members/new`,`/members` `POST`
* **회원** 조회 --> `/members/{id}` `GET`
* **회원** 수정 폼 --> `/members/{id}/edit` `GET`
* **회원** 수정 --> `/members/{id}/edit`,`/members/{id}` `POST`
* **회원** 삭제 --> `/members/{id}/delete` `POST`

위처럼 url 정보에 어떤 행동을 할 것인지를 명시하고 GET 과 POST 를 사용하여 처리하는 방법이 있습니다. 이것을 컨트롤 URI(컨트롤러) 라고 합니다. 