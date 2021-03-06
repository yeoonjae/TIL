> ๐ ๋ณธ ๊ธ์ ์ธํ๋ฐ ๊ฐ์ '์คํ๋ง ํต์ฌ ๊ธฐ์ '์ ๋ฃ๊ณ  ์ ๋ฆฌํ ๊ธ์๋๋ค. 

<br>


# **ReSource** 
 Spring์ Resource ๊ฐ์ฒด๋ `java.net.URL`์ ์ถ์ํ ํ ๊ฒ์ผ๋ก Resource์ ๋ํ ์ ๊ทผ์ ์ถ์ํํ๊ธฐ ์ํ ์ธํฐํ์ด์ค์๋๋ค. 

---
## **Resource๋ฅผ ์ถ์ํ ํ ์ด์ **
* `java.net.URL`์๋ classPath ๊ธฐ์ค์ผ๋ก ๋ฆฌ์์ค๋ฅผ ์ฝ์ด์ค๋ ๊ธฐ๋ฅ ๋ถ์ฌ
* ServletContext ๋ฅผ ๊ธฐ์ค์ผ๋ก ์๋ ๊ฒฝ๋ก๋ก ์ฝ์ด์ค๋ ๊ธฐ๋ฅ ๋ถ์ฌ
* ์๋ก์ด ํธ๋ค๋ฌ๋ฅผ ๋ฑ๋กํ์ฌ ํน๋ณํ URL ์ ๋ฏธ์ฌ๋ฅผ ๋ง๋ค์ด ์ฌ์ฉํ  ์๋ ์์ง๋ง ๊ตฌํ์ด ๋ณต์กํ๊ณ  ํธ์์ฑ ๋ฉ์๋ ๋ถ์กฑ

`java.net.URL` ํด๋์ค๋ http,ftp,file ๊ณผ ๊ฐ์ ์ ๋์ด(prefix)๋ฅผ ์ง์ ํ  ์ ์์ด์ ๋ค์ํ ์๊ฒฉ ๋ฆฌ์์ค์ ์ ๊ทผ์ด ๊ฐ๋ฅํฉ๋๋ค. ํ์ง๋ง, classPath ์์ ์กด์ฌํ๋ ๋ฆฌ์์ค๋ ServletContext์ ๋ฆฌ์์ค๋ฅผ ์ง์ ํ  ๋ฐฉ๋ฒ์ด ์๋ค๋ ์ ๊ณผ ํ์ผ์ ์กด์ฌ ์ฌ๋ถ๋ฅผ ๋ฏธ๋ฆฌ ํ์ธํ  ์ ์๋ค๋ ๋จ์ ์ด ์์ต๋๋ค. 

Resource ์ธํฐํ์ด์ค๋ฅผ ์ดํด๋ณด๋๋ก ํ๊ฒ ์ต๋๋ค. 
 
```java
package org.springframework.core.io;

public interface Resource extends InputStreamSource {
    // ๋ผ์์ค์ ์กด์ฌ ์ฌ๋ถ ํ์ธ (ํญ์ ์๋ค๋ ๋ณด์ฅ์ด ์๊ธฐ ๋๋ฌธ์ ์ฌ์ฉ)
    boolean exists();
    // ์ฝ์ ์ ์๋์ง ํ์ธ
    default boolean isReadable() {
        return this.exists();
    }
    // ํ์ฌ ๋ฆฌ์์ค์ ๋ํ ์๋ ฅ ์คํธ๋ฆผ์ด ์ด๋ ค์๋ ์ง ํ์ธ 
    default boolean isOpen() {
        return false;
    }

    default boolean isFile() {
        return false;
    }
    
    // JDK์ URL,URI,File ํํ๋ก ์ ํ ๊ฐ๋ฅํ ๋ฆฌ์์ค์ ์ ์ฉ
    URL getURL() throws IOException;
    URI getURI() throws IOException;
    File getFile() throws IOException;

    default ReadableByteChannel readableChannel() throws IOException {
        return Channels.newChannel(this.getInputStream());
    }

    // ๋ฆฌ์์ค์ ๋ํ ์ด๋ฆ๊ณผ ๋ถ๊ฐ์ ์ธ ์ ๋ณด๋ฅผ ์ ๊ณต
    long contentLength() throws IOException;
    long lastModified() throws IOException;
    Resource createRelative(String relativePath) throws IOException;
    @Nullable
    String getFilename();

    // ์ ์ฒด ๊ฒฝ๋ก ํฌํจํ ํ์ผ ์ด๋ฆ ๋๋ ์ค์  URL
    String getDescription(); 
}

```

  ์คํ๋ง IoC ์ปจํ์ด๋๊ฐ ์์ฑ๋  ๋๋ถํฐ ์์ํ์ฌ ์ปจํ์ด๋ ์ค์  ์ ๋ณด๋ฅผ ๋ด๋ ํ์ผ๋ค์ ๊ฐ์ ธ์ฌ ๋, ์คํ๋ง์ ๊ฑฐ์ ๋ชจ๋  API๋ ์ธ๋ถ์ ๋ฆฌ์์ค ์ ๋ณด๊ฐ ํ์ํ  ๋๋ ํญ์ ์ด Resource ์ถ์ํ๋ฅผ ์ฌ์ฉํฉ๋๋ค. 
 
 ```java
 // classPath ๊ธฐ์ค์ผ๋ก 000.xml์ด๋ผ๋ ๋ฌธ์์ด์ ํด๋นํ๋ ๋ฆฌ์์ค๋ฅผ ์ฐพ์์ ๋น ์ค์ ํ์ผ๋ก ๋ฑ๋ก
 var ctx = new ClassPathXmlApplicationContext("000.xml");
 // ํ์ผ ์์คํ ๊ฒฝ๋ก๋ฅผ ๊ธฐ์ค์ผ๋ก 000.xml์ด๋ผ๋ ๋ฌธ์์ด์ ํด๋นํ๋ ๋ฆฌ์์ค๋ฅผ ์ฐพ์์ ๋น ์ค์ ํ์ผ๋ก ๋ฑ๋ก
 var ctx = new FileSystemXmlApplicationContext("000.xml");

 Resource resource = resourceLoader.getResource("classPath:text.xml");
 ```

--- 
## **Resource ์ ๊ตฌํ์ฒด**
 * UrlResource : `java.net.URL` ์ฐธ๊ณ , ๊ธฐ๋ณธ์ผ๋ก ์ง์ํ๋ ํ๋กํ ์ฝ http, https, ftp, file, jar
 * ServletContextResource : ์น ์ ํ๋ฆฌ์ผ์ด์ ๋ฃจํธ์์ ์๋ ๊ฒฝ๋ก๋ก ๋ฆฌ์์ค๋ฅผ ์ฐพ๋๋ค. ์ฌ์ค์ Spring์์ ๊ฐ์ฅ ๋ง์ด ์ฌ์ฉํ๊ฒ ๋๋ ๊ตฌํ์ฒด์๋๋ค. ์ด์ ๋ ์ฝ์ด๋ค์ด๋ Resource ํ์์ด ApplicationContext ๊ณผ ๊ด๋ จ์ด ์๊ธฐ ๋๋ฌธ์๋๋ค. 



## **Resource ์ฝ์ด์ค๊ธฐ**
 * Resource ํ์์ location ๋ฌธ์์ฌ๋ก๊ฐ `ApplicationContext`์ ํ์์ ๋ฐ๋ผ ๊ฒฐ์ ๋ฉ๋๋ค. 
    * ClassPathXmlApplicationContext -> ClassPathResource
    * FileSystemXmlApplicationContext -> FileSystemResource
    * WebApplicationContext -> ServletContextResource

 * **<span style="color:blue">ApplicationContext์ ํ์์ ์๊ด์์ด ๋ฆฌ์์ค ํ์์ ๊ฐ์ ํ๋ ค๋ฉด `java.net.URL` ์ ๋์ด(+ `classpath:`)์ค ํ๋๋ฅผ ์ฌ์ฉํ  ์ ์์ต๋๋ค.</span>**
    - classpath:me/whiteship/config.xml -> ClassPathResource
    - file:///some/resource/path/config.xml -> FileSystemResourc


```java
@Component
public class AppRunner implements ApplicationRunner {
    @Autowired
    ApplicationContext resourceLoader;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(resourceLoader.getClass()); // ApplicationContext์ class๋ณด๊ธฐ

        Resource resource = resourceLoader.getResource("text.txt"); // ์๋ฌด๋ฐ ์ ๋์ด๋ฅผ ๋ถ์ด์ง ์์์ ๋์ resource
        Resource resourceClassPath = resourceLoader.getResource("classpath:text.txt"); // classpath ์ ๋์ด๋ฅผ ๋ถ์ธ resource
        System.out.println(resource.getClass()); // ServletContextResource
        System.out.println(resourceClassPath.getClass()); // ClassPathResource
    }
}
```
> ์คํ๊ฒฐ๊ณผ <br>
class org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext<br>
class org.springframework.web.context.support.ServletContextResource<br>
class org.springframework.core.io.ClassPathResource



---

# **ResourceLoader**
Spring ์๋ URL ํด๋์ค์ ์ ์ฌํ๊ฒ ์ ๋์ด๋ฅผ ์ด์ฉํด Resource ์ค๋ธ์ ํธ๋ฅผ ์ ์ธํ๋ ๋ฐฉ๋ฒ์ด ์์ต๋๋ค. ๋ฌธ์์ด ์์ ๋ฆฌ์์ค์ ์ข๋ฅ์ ๋ฆฌ์์ค์ ์์น๋ฅผ ํจ๊ป ํํํ๊ฒ ํด์ฃผ๋ ๊ฒ์๋๋ค. ๊ทธ๋ฆฌ๊ณ  ์ด๋ ๊ฒ ๋ฌธ์์ด๋ก ์ ์๋ ๋ฆฌ์์ค๋ฅผ ์ค์  Resource ํ์ ์ค๋ธ์ ํธ๋ก ๋ณํํด์ฃผ๋ ResourceLoader๋ฅผ ์ ๊ณตํฉ๋๋ค. ResourceLoader ๋ ๊ตฌํ์ด ๋ค์ํ  ์ ์์ผ๋ฏ๋ก ์ธํฐํ์ด์ค๋ก ์ ์๋์ด ์์ต๋๋ค. 

---
## **ResourceLoader์ ๊ธฐ๋ฅ**
* ํ์ผ ์์คํ์์ ์ฝ์ด์ค๊ธฐ
* ํด๋์คํจ์ค์์ ์ฝ์ด์ค๊ธฐ
* URL๋ก ์ฝ์ด์ค๊ธฐ
* ์๋/์ ๋ ๊ฒฝ๋ก๋ก ์ฝ์ด์ค๊ธฐ

---
## **ResourceLoader๊ฐ ์ฒ๋ฆฌํ๋ ์ ๋์ด์ ์**

|์ ๋์ด|์|์ค๋ช|
|------|---|---|
|file:|file:/C:/temp/file.txt|ํ์ผ ์์คํ์ C:/temp ํด๋์ ์๋ file.txt๋ฅผ ๋ฆฌ์์ค๋ก ๋ง๋ค์ด์ค๋ค.|
|classpath:|classpath:file.txt|ํด๋์คํจ์ค ๋ฃจํธ์ ์กด์ฌํ๋ file.txt ๋ฆฌ์์ค์ ์ ๊ทผํ๊ฒ ํด์ค๋ค. |
|์์|WEB-INF/text.dat|์ ๋์ด๊ฐ ์๋ ๊ฒฝ์ฐ์๋ ResourceLoader๊ตฌํ์ ๋ฐ๋ผ ๋ฆฌ์์ค์ ์์น๊ฐ ๊ฒฐ์ ๋๋ค. `ServletResourceLoader`๋ผ๋ฉด ์๋ธ๋ฆฟ ์ปจํ์คํธ์ ๋ฃจํธ๋ฅผ ๊ธฐ์ค์ผ๋ก ํด์ํ๋ค. |
|http:|http://www.myserver.com/text.dat|HTTP ํ๋กํ ์ฝ์ ์ฌ์ฉํด ์ ๊ทผํ  ์ ์๋ ์น์์ ๋ฆฌ์์ค๋ฅผ ์ง์ ํ๋ค. ftp:๋ ์ฌ์ฉํ  ์ ์๋ค. |
----



## **์ ๋ฆฌํ๊ธฐ**

ApplicationContext ๋ ResourceLoader๋ฅผ ๊ตฌํํ ๊ตฌํ์ฒด๋ก getResource("๋ฆฌ์์ค๋ช") ๋ฉ์๋๋ฅผ ์ฌ์ฉํ์ฌ resource ํ์์ ๋ฐํํ  ์ ์์ต๋๋ค. 

๋ฐํ๋๋ Resource๋ Reosurce๋ฅผ ๊ตฌํํ ๊ตฌํ์ฒด `ClassPathResource`, `ServletContextResource`, `FileSystemResource` ์ค ํ๋๋ฅผ ๋ฐํํ๋ฉฐ, Spring Boot๋ `ApplicationContext`๋ฅผ ๊ธฐ๋ณธ์ ์ผ๋ก Servlet ๊ธฐ๋ฐ์ `WebApplicationContext` ๋ผ์ `ServletContextResource`๋ฅผ ๋ฐํํฉ๋๋ค. 

๋ง์ฝ `ServletContextResource`๊ฐ ์๋ `ClassPathResource`๋ฅผ ๋ฐํ์ํค๊ณ  ์ถ๋ค๋ฉด `classpath:` ์ ๋์ด๋ฅผ ์ฌ์ฉํ์ฌ ๋ช์ํด์ฃผ์ด์ผ ํฉ๋๋ค.

---

    ๊ฐ์๋ฅผ ๋ค์ผ๋ฉฐ ์ ๋ฆฌํ ๋ด์ฉ์ด์ง๋ง, ๋ง๊ฒ ์ดํดํ ๊ฒ์ธ์ง ํ์ ์ด ์์ง ์์ต๋๋ค... 
    ์ถํ์ ๊ณต๋ถ๋ฅผ ํตํด ํ๋ฆฐ ๋ด์ฉ์ด ์๋ค๋ฉด ์์ ํ๋๋ก ํ๊ฒ ์ต๋๋ค. 

