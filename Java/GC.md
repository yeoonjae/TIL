# 👀 **Java의 GC 쉽게 이해하기**

## **GC란** 
<br>

 우리는 Java 프로그래밍을 하면서 `new`라는 키워드를 사용하여 객체를 생성합니다. 이 때 `new`가 붙은 객체는 Heap 영역에 동적으로 메모리를 할당해줍니다. 이렇게 동적으로 할당된 메모리는 더 이상 사용하지 않으면 해제되어야 합니다. 해제하지 않으면 메모리를 계속해서 점유하고 있으므로 `OutOfMemmory Exception` 이 발생할 수 있습니다. 

이렇게 동적으로 할당된 메모리를 해제해주는 작업을 하는 것이 GC입니다. 정리하자면 다음과 같습니다. 

    GC란 Garbage Collection(이하 GC)의 약자로 더 이상 사용하지 않는(쓰레기) 객체를 찾아 지우는 작업을 한다.

Java는 GC가 메모리를 자동으로 관리해줍니다. 만약 GC가 없었다면 개발자가 수동으로 사용하지 않는 메모리를 체크하고 반환하는 일을 수행해야 합니다. 

생각만 해도 번거롭습니다. 자동으로 메모리를 관리한다는 것이 어떤 관점에서 보면 비효율적이어 보일 수도 있지만, 개발자 입장에선 개발에 집중할 수 있는 환경을 만들어 준다는 장점이 있습니다. 
    
    💡 Java에서도 GC를 개발자가 직접 호출하는 방법도 있습니다. 바로 System.gc() 메소드 입니다. 하지만 해당 메소드는 시스템의 성능에 큰 영향을 미치므로 절대 호출해서는 안됩니다. 그냥 GC가 알아서 동작하도록 놔두는 것이 제일 베스트입니다. 


* * * 

## **Stop-the-world**
<br>
stop-the-world란, GC를 실행하기 위해 JVM이 어플리케이션의 실행을 멈추는 것 입니다. GC를 수행하는 메소드를 제외한 모든 쓰레드가 일시적으로 정지되며, GC가 끝난 뒤에야 정지됐던 쓰레드가 정상적으로 동작합니다. 


어떠한 알고리즘을 사용하여 GC를 수행하든 Stop-and-world 상태를 맞이하게 됩니다. GC 튜닝이라하면 이와 같은 쓰레드의 작업이 멈추는 시간(Stop-and-world)을 최소한으로 줄이는 것이라고 보면 됩니다. 

<br>

* * * 
## **Reachability**
<br>

Java 의 GC는 `Reachability` 의 개념을 알아야합니다. 어떠한 객체에 유효한 참조가 있다면 `reachable` 이라하고, 유효한 참조가 없다면 `unreachable`이라 합니다. 

GC에는 GC root가 존재합니다. GC root는 말 그대로 GC의 뿌리라고 이해하면 됩니다. 
    
    ✔ GC root가 되는 조건
    1. stack 영역의 데이터(지역변수, 파라미터 등)
    2. method 영역의 static 데이터
    3. 실행중인 쓰레드
    4. JNI (Java Native Interface)에 의해 생성된 객체

![image](https://user-images.githubusercontent.com/63777714/137363612-c840618c-c0ff-4fec-a4d4-fe0e1eac1f37.png)

위의 사진에서 보이는 것 처럼 GC Root와 연결된 객체는 `reachable` 그 외는 `unreachable` 이라고 합니다. 아래의 그림을 통해 이해를 도와보도록 하겠습니다. 

![image](https://user-images.githubusercontent.com/63777714/137363099-562e972f-e816-4617-9de0-ee207f281297.png)

위의 사진에서 1,3,4,5 는 `reachable`한 상태입니다. 
* 1,5 는 Stack 영역에 있는 6, 7을 참조하고 있습니다. 
* 3 은 Method Area 영역에 있는 8을 참조하고 있습니다. 
* 4 는 7을 참조하고 있는 5를 참조하고 있습니다.

`unreachable` 한 2 는 아무것도 참조하고 있지 않습니다. 즉, GC의 수거 대상이 됩니다. 

* * *
## 🤷‍♂️ **그럼 GC의 어떤 알고리즘에 의해 동작하지?**
<br>

바로 `Mark and Sweep` 이라는 알고리즘으로 동작합니다. 

 GC root에서 시작하여 root가 참조하는 모든 객체 또는 그 객체들이 참조하는 다른 객체들을 탐색하며 마크(Mark)합니다. 이 과정이 `Mark and Sweep` 알고리즘의 `Mark`에 해당합니다. 

![image](https://user-images.githubusercontent.com/63777714/137359624-54ac5e98-f295-4540-ab5b-b6854824479e.png)
<br>[참조된 개체 : <span style="color:blue">파란색</span> / 참조되지 않은 개체 : <span style="color:yellow">노란색</span>]


`Mark`가 끝나면 GC는 마크(Mark)되지 않은 메모리들을 해제합니다. 이 과정을 `Sweep`이라고 합니다. 

![image](https://user-images.githubusercontent.com/63777714/137360392-de6c9278-9479-4645-8ff2-972eaa29d696.png)
<br>[참조되고 있지 않은 개체를 삭제 한 후의 그림입니다. ]


`Mark and Sweep` 알고리즘 뿐만 아니라 `compact` 라는 알고리즘도 있습니다.   `compact`는 `Mark and Sweep` 과정을 거치고 나면 Mark 되지 않은 객체들만 남아있을 것 입니다. 그러면 객체들이 듬성듬성 존재하겠죠? 이러한 객체들을 압축, 즉 한 곳으로 모아서 메모리 단편화를 막습니다. `compact` 과정을 통해 새 메모리 할당이 쉽고, 빨라지도록 합니다. 

![image](https://user-images.githubusercontent.com/63777714/137360656-bbab7ba7-bad9-459a-82d2-2a8c7d87a952.png)
<br>[Sweep 과정 후 듬성듬성 존재하는 참조된 개체 압축한 모습]


* * * 
## 🔍 **그래서 저 알고리즘이 언제 동작하는데?**
<br>

GC는 Heap 영역에 동적으로 할당된 메모리 중 더 이상 사용하지 않는 메모리를 자동으로 해제해준다고 했습니다.

Heap 영역에 대해 알아보도록 하죠. Heap 영역은 아래의 사진과 같이 나뉩니다. 

![image](https://user-images.githubusercontent.com/63777714/137364486-36f48f65-4334-4912-8006-150d30360344.png)

* Young Generation : 젊은 객체
    * eden : 객체를 생성하자마자 저장되는 장소
    * Survivor Space 0 : 감시자 0
    * Survivor Space 1 : 감시자 2
* Old Generation : 오래된 객체
* Parameter Generation

크게 `Young Generation`, `Old Generation`, `Parameter Generation` 으로 나뉘며 `Young Generation` 은 `eden` , `Survivor Space 0` , `Survivor Space 1` 로 또 나뉩니다. 

Heap 영역 메모리가 어떻게 나뉘는지 살펴봤으니 이제 Heap 영역에 객체가 생성되고 GC가 일어나는 과정을 살펴보도록 하겠습니다. 우리가 객체를 생성하자마자 저장되는 장소는 Eden 영역입니다.

1. Eden 영역에 객체가 생성됩니다. 
2. Eden 영역이 꽉 차면 Minor GC(마이너 GC)가 발생(Mark-and-Sweep)합니다. 
3. 살아있는 객체만 Survivor 0 영역으로 복사되고,
       다시 Eden 영역을 채우게 됩니다. 
4. Survivor 0 영역이 꽉 차게 되면 Survivor 1 영역으로 객체가 복사됩니다.
       이 때, Eden 영역에 있는 객체들 중 살아있는 객체들도 Survivor 1 영역으로 갑니다. 
       즉, Survivor 0 또는 1 둘 중 하나는 반드시 비워져 있어야 합니다. 
    
5. 1~4가 반복될 때마다 살아있는 객체는 age값이 증가합니다. 
       일정 수준의 age값에 도달하면 Young Generation에서 Old Generation으로 이동합니다. 
6. 1~5가 반복하다보면 Old Generation도 꽉 차게 됩니다. 이 때 major GC(메이저 GC) 가 발생하게 됩니다. (Full GC 라고도 불린다.)

위의 과정을 사진을 통해 이해해 보도록 하겠습니다. 

![image](https://user-images.githubusercontent.com/63777714/137370417-a52a3c64-46b0-44d2-87ca-6b31dcf57acd.png)

위의 사진은 1~4 까지의 과정입니다. 위의 과정을 반복하며 오래된 객체(age값으로 판단)는 Old Generation으로 이동하게 됩니다. (아래의 사진 참고)

![image](https://user-images.githubusercontent.com/63777714/137370863-fa664af2-4123-4c23-b404-d6118df4ca28.png)

그렇게 Old Generation까지 꽉차면 메이저 GC가 발생하게 되는 것 입니다. 


    💡 minor GC vs major GC 누가 더 빠를까?
     --> 답은 minor GC!! 
     이유는 일반적으로 더 작은 공간이 할당되고, 객체들을 처리하는 방식도 다르기 때문입니다. 

* * * 

## 🤷‍♂️ **왜 GC를 나누나요..**
<br>

그냥 한 번에 GC를 진행하면 될 것을 왜 나누었을까요?

GC 설계자들은 두 가지의 가설을 전재하고 GC를 만들었습니다. 
* 대부분의 객체는 금방 접근 불가능한 상태(unreachable)가 된다. 즉, <u>금방 garbage가 된다.</u>
* 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다. 

그리고, oracle 사이트에 있는 GC의 기본사항을 보면 다음과 같이 적혀져 있습니다. 
> JVM의 모든 개체를 표시하고 압축해야 하는 것은 비효율적입니다. 더 많은 개체가 할당됨에  따라 개체 목록이 늘어나고 가비지 수집 시간이 점점 더 길어집니다. 그러나 응용  프로그램에 대한 경험적 분석에 따르면 대부분의 개체는 수명이 짧습니다. 

위의 어려운 내용을 한 줄로 요약하자면, 금방 garbage가 되어서 GC의 대상이 되니 한 번에 하는 것 보다 세대별로 나누어서 GC를 수행하는 것이 더 효율적이라는 말 입니다. 
* * *

이번 글에서 GC의 종류까지 다룰 생각이었지만, 생각보다 글이 길어져 GC의 종류에 대해서는 다음 글에서 다루겠습니다. 

> 마지막 한마디.. <br>
> 공식문서를 보는 법을 아주 사아아알짝 알 것 같습니다. 다른 블로그를 참고하는 것 보다 공식문서를 보고 이해하는 것이 더 쉬웠습니다. 물론 유튜브를 보며 이해를 도왔습니다 ㅎㅎ

* * * 
References
* https://www.youtube.com/watch?v=Fe3TVCEJhzo
* https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html