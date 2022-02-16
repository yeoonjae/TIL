# **String과 StringBuffer, StringBuilder의 차이점** <br>
Java에선 문자열을 다루는 클래스가 대표적으로 `String`, `StringBuffer`,`StringBuilder` 가 있습니다. 

그렇다면 어떤 경우에 어떤 클래스를 사용하는 것이 적절한지 알아보도록 하겠습니다. 

## **String**
String 객체는 immutable 한 객체입니다. 
```java
String str = "hello";
```
다음과 같이 문자열을 생성할 경우 StringPool이라는 영역에 str이라는 이름으로 hello라는 메모리 영역을 가르키는 문자열을 저장합니다. 

그 이후 
```java
str = "hello world";
```
다음과 같이 선언하면 기존의 영역에 world를 추가한 문자열을 저장하는 것이 아닌 hello world 라는 새로운 문자열을 저장합니다. 그림으로 보면 다음과 같습니다. 

![image](https://user-images.githubusercontent.com/63777714/154247257-91bfcd4e-9036-4e76-9d6a-b2e6000ba9f6.png)

이와 같이 String은 불변성을 가지기 떄문에 문자열의 변경이 잦을 경우 메모리에 참조가 없는 많은 Garbage가 생성되기 떄문에 힙 메모리가 부족하여 프로그램에 치명적인 영향을 끼칠 수 있습니다. 

## **StringBuffer 와 StringBuilder**
위와 같은 문제를 해결하기 위해서 가변성을 가지는 StringBuffer와 StringBuiler를 도입하였습니다. 

StringBuffer와 StringBuilder를 사용하면 동일한 문자열에 대해 `append()`를 이용하여 문자열을 추가할 수 있고, `delete()`를 사용하여 문자열을 삭제할 수도 있습니다. 

![image](https://user-images.githubusercontent.com/63777714/154248110-ee181932-4587-4b82-8488-23a0ff54dff0.png)

따라서 문자열 연산이 잦은 작업을 할 때에는 String 클래스보다 StringBuffer 또는 StringBuilder 를 사용하는 것이 메모리 상 효율적입니다. 

## **StringBuffer vs StringBuiler**

그렇다면 둘의 차이점은 무엇일까요? 바로 동기화의 유무입니다. 다음은 StringBuffer 클래스의 일부입니다.


```java
 public final class StringBuffer
    extends AbstractStringBuilder
    implements java.io.Serializable, Comparable<StringBuffer>, CharSequence
{
    ...
        @Override
    public synchronized int compareTo(StringBuffer another) {
        return super.compareTo(another);
    }

    @Override
    public synchronized int length() {
        return count;
    }

    @Override
    public synchronized int capacity() {
        return super.capacity();
    }


    @Override
    public synchronized void ensureCapacity(int minimumCapacity) {
        super.ensureCapacity(minimumCapacity);
    }
...
}
```

메소드에 `synchronized` 키워드가 붙어있는 것을 확인할 수 있습니다. 따라서 StringBuffer는 동기화를 보장해주는 키워드를 사용함으로써 멀티쓰레드 환경에서 Thread-safe를 보장해줍니다. 

그에 반해 StringBuilder는 동기화 키워드를 사용하지 않습니다. 따라서 StringBuffer 보다는 속도가 더 빠르지만, 멀티쓰레드 환경에서 Thread-safe하지 않습니다. 




---

## **정리**
* String은 문자열 연산이 적고, 멀티쓰레드 환경일 경우에 사용
* StringBuffer는 문자열 연산이 많고 멀티쓰레드 환경일 경우에 사용
* StringBuilder는 문자열 연산이 많고, 동기화를 고려하지 않아도 될 때 사용하는 것이 좋습니다. 

