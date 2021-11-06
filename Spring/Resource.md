> 📚 본 글은 인프런 강의 '스프링 핵심 기술'을 듣고 정리한 글입니다. 

<br>


# **ReSource** 
 Spring의 Resource 객체는 `java.net.URL`을 추상화 한 것으로 Resource에 대한 접근을 추상화하기 위한 인터페이스입니다. 

---
## **Resource를 추상화 한 이유**
* `java.net.URL`에는 classPath 기준으로 리소스를 읽어오는 기능 부재
* ServletContext 를 기준으로 상대 경로로 읽어오는 기능 부재
* 새로운 핸들러를 등록하여 특별한 URL 접미사를 만들어 사용할 수는 있지만 구현이 복잡하고 편의성 메소드 부족

`java.net.URL` 클래스는 http,ftp,file 과 같은 접두어(prefix)를 지정할 수 있어서 다양한 원격 리소스에 접근이 가능합니다. 하지만, classPath 안에 존재하는 리소스나 ServletContext의 리소스를 지정할 방법이 없다는 점과 파일의 존재 여부를 미리 확인할 수 없다는 단점이 있습니다. 

Resource 인터페이스를 살펴보도록 하겠습니다. 
 
```java
package org.springframework.core.io;

public interface Resource extends InputStreamSource {
    // 라소스의 존재 여부 확인 (항상 있다는 보장이 없기 때문에 사용)
    boolean exists();
    // 읽을 수 있는지 확인
    default boolean isReadable() {
        return this.exists();
    }
    // 현재 리소스에 대한 입력 스트림이 열려있는 지 확인 
    default boolean isOpen() {
        return false;
    }

    default boolean isFile() {
        return false;
    }
    
    // JDK의 URL,URI,File 형태로 전환 가능한 리소스에 적용
    URL getURL() throws IOException;
    URI getURI() throws IOException;
    File getFile() throws IOException;

    default ReadableByteChannel readableChannel() throws IOException {
        return Channels.newChannel(this.getInputStream());
    }

    // 리소스에 대한 이름과 부가적인 정보를 제공
    long contentLength() throws IOException;
    long lastModified() throws IOException;
    Resource createRelative(String relativePath) throws IOException;
    @Nullable
    String getFilename();

    // 전체 경로 포함한 파일 이름 또는 실제 URL
    String getDescription(); 
}

```

  스프링 IoC 컨테이너가 생성될 때부터 시작하여 컨테이너 설정 정보를 담는 파일들을 가져올 때, 스프링의 거의 모든 API는 외부의 리소스 정보가 필요할 때는 항상 이 Resource 추상화를 사용합니다. 
 
 ```java
 // classPath 기준으로 000.xml이라는 문자열에 해당하는 리소스를 찾아서 빈 설정파일로 등록
 var ctx = new ClassPathXmlApplicationContext("000.xml");
 // 파일 시스템 경로를 기준으로 000.xml이라는 문자열에 해당하는 리소스를 찾아서 빈 설정파일로 등록
 var ctx = new FileSystemXmlApplicationContext("000.xml");

 Resource resource = resourceLoader.getResource("classPath:text.xml");
 ```

--- 
## **Resource 의 구현체**
 * UrlResource : `java.net.URL` 참고, 기본으로 지원하는 프로토콜 http, https, ftp, file, jar
 * ServletContextResource : 웹 애플리케이션 루트에서 상대 경로로 리소스를 찾는다. 사실상 Spring에서 가장 많이 사용하게 되는 구현체입니다. 이유는 읽어들이는 Resource 타입이 ApplicationContext 과 관련이 있기 떄문입니다. 



## **Resource 읽어오기**
 * Resource 타입은 location 문자여로가 `ApplicationContext`의 타입에 따라 결정됩니다. 
    * ClassPathXmlApplicationContext -> ClassPathResource
    * FileSystemXmlApplicationContext -> FileSystemResource
    * WebApplicationContext -> ServletContextResource

 * **<span style="color:blue">ApplicationContext의 타입에 상관없이 리소스 타입을 강제하려면 `java.net.URL` 접두어(+ `classpath:`)중 하나를 사용할 수 있습니다.</span>**
    - classpath:me/whiteship/config.xml -> ClassPathResource
    - file:///some/resource/path/config.xml -> FileSystemResourc





---

# **ResourceLoader**
Spring 에는 URL 클래스와 유사하게 접두어를 이용해 Resource 오브젝트를 선언하는 방법이 있습니다. 문자열 안에 리소스의 종류와 리소스의 위치를 함께 표현하게 해주는 것입니다. 그리고 이렇게 문자열로 정의된 리소스를 실제 Resource 타입 오브젝트로 변환해주는 ResourceLoader를 제공합니다. ResourceLoader 도 구현이 다양할 수 있으므로 인터페이스로 정의되어 있습니다. 

---
## **ResourceLoader의 기능**
* 파일 시스템에서 읽어오기
* 클래스패스에서 읽어오기
* URL로 읽어오기
* 상대/절대 경로로 읽어오기

---
## **ResourceLoader가 처리하는 접두어의 예**

|접두어|예|설명|
|------|---|---|
|file:|file:/C:/temp/file.txt|파일 시스템의 C:/temp 폴더에 있는 file.txt를 리소스로 만들어준다.|
|classpath:|classpath:file.txt|클래스패스 루트에 존재하는 file.txt 리소스에 접근하게 해준다. |
|없음|WEB-INF/text.dat|접두어가 없는 경우에는 ResourceLoader구현에 따라 리소스의 위치가 결정된다. `ServletResourceLoader`라면 서블릿 컨텍스트의 루트를 기준으로 해석한다. |
|http:|http://www.myserver.com/text.dat|HTTP 프로토콜을 사용해 접근할 수 있는 웹상의 리소스를 지정한다. ftp:도 사용할 수 있다. |
----



## **정리하기**

ApplicationContext 는 ResourceLoader를 구현한 구현체로 getResource("리소스명") 메소드를 사용하여 resource 타입을 반환할 수 있습니다. 

반환되는 Resource는 Reosurce를 구현한 구현체 `ClassPathResource`, `ServletContextResource`, `FileSystemResource` 중 하나를 반환하며, Spring Boot는 `ApplicationContext`를 기본적으로 Servlet 기반의 `WebApplicationContext` 라서 `ServletContextResource`를 반환합니다. 

만약 `ServletContextResource`가 아닌 `ClassPathResource`를 반환시키고 싶다면 `classpath:` 접두어를 사용하여 명시해주어야 합니다.

---

    강의를 들으며 정리한 내용이지만, 맞게 이해한 것인지 확신이 서진 않습니다... 
    추후에 공부를 통해 틀린 내용이 있다면 수정하도록 하겠습니다. 

