# 👀 **GC의 종류**

이전글 :  <a href = "https://github.com/yeoonjae/TIL/blob/main/Java/GC.md">GC 이해하기</a>

<hr>
이번 글에서는 GC의 종류에 대해서 알아보겠습니다. 
<br>

## 🔍 **Serial GC**

* mark-compact 알고리즘을 사용
* GC 모두 직렬로 수행(단일 가상 CPU 사용) - 싱글 쓰레드 사용
* stop-the-world 시간이 김
* 가장 오래된 GC

* * * 
## 🔍 **Parallel GC**

* Mark-Sweep-Compaction 알고리즘을 사용
* Java 8의 default GC
* Young 영역에서 멀티 쓰레드를 사용하여 GC를 수행
* stop-the-world 시간이 Serial GC에 비해 감소

* * * 
## 🔍 **Parallel Old GC**

* Mark-Summary-Compaction 알고리즘을 사용
    * Sweep : 단일 쓰레드가 old 영역 전체를 훑는다.
    * Summary : 멀티 쓰레드가 old 영역을 분리해서 훑는다. 
* Old 영역도 멀티 쓰레드를 사용하여 GC를 수행

* * * 
## 🔍 **CMS(Concurrent Mark Sweep) GC**

* Stop-the-world 시간을 최소화 하기 위해 고안된 GC
* CMS의 수집 단계는 다음과 같습니다.
    1. Initial Mark (`Stop the World Event`)
        * GC Root 에서 참조하는 객채들만 우선 탐색하며, 참조 여부를 파악한다. 
        * 위 과정에서 Stop-the-world가 발생하지만, Stop-the-world 시간이 짧다. 
    2. Concurrent Marking
        * Initial Mark 과정에서 GC 의 대상으로 판별한 객체들을 따라가며 GC의 대상인지 추가로 확인한다.
        * Stop-the-world가 발생하지 않는다. 
    3. Remark (`Stop the World Event`)
        * Concurrent Marking 단계의 결과를 검증한다. 
        * 쓰레드의 업데이트로 인해 누락된 객체가 없는지, 참조가 제거되었는지 확인한다.
        * Stop-the-world 가 발생하며, 이 시간을 최대한 줄이기 위해 멀티스레드로 검증작업을 수행한다.
    4. Concurrent Sweep
        * GC 의 대상으로 식별된 객체를 모두 제거합니다. 
        * Stop-the-world 가 발생하지 않는다. 
    5. Resetting
        * 데이터 구조를 지워다음 동시 수집을 준비합니다. 

* 위와 같은 과정을 거치다 보니, CPU 부하가 커지는 단점이 존재합니다.
* Compaction 이 기본적으로 제공되지 않고, 필요할 때만 일어나서 Stop-the-world 시간이 다른 GC보다 더 길게 걸릴 수도 있습니다. 

* * * 
## 🔍 **G1 GC (Garbage First)**
* G1 GC 는 대용량 메모리가 있는 다중 프로세서 시스템을 대상으로 하는 GC 입니다. 
* G1이 Compaction이 제공되며 Compaction이 필요할때만 일어나는 CMS 의 단점을 보완합니다. 
* G1은 CMS 수집기보다 예측 가능한 가비지 수집 일시 중지를 제공하며 사용자가 원하는 일시 중지 대상을 지정할 수 있습니다.
* 이전 GC와 G1 GC의 힙 구조를 살펴보면 다음과 같습니다. 

![image](https://user-images.githubusercontent.com/63777714/137593481-249faa01-035d-4716-8ce6-d87f83b4f91b.png)

이전 GC는 eden,survivor,old 영역이 나눠져 있지만, 고정된 크기로 존재하진 않습니다. 반면, G1 GC는 고정된 크기로 Region으로 분할됩니다. 즉, 전체 Heap 영역이 아닌, Region 단위로 탐색합니다. <u>이는 메모리 사용에 있어 큰 유연성을 제공</u>합니다. 

* Garbage만 존재하는 Region을 수거해 감으로써, Garbage First라고 불립니다. 
* G1은 회수 가능한 개체, 즉 쓰레기로 가득 차 있을 가능성이 있는 힙 영역에 수집 및 압축 작업을 집중합니다. 
* Full GC 가 일어나면 수집 단계는 다음과 같습니다. 
    1. Initial Mark
        * Old Region에 존재하는 객체들이 참조하는 Survivor Region을 찾습니다. 
        * 이 과정에서 Stop-The-World가 발생하게 됩니다.
    2. Root Region Scan
        * Initial Mark 에서 찾은 Survivor Region에서 GC 대상 객체를 탐색합니다.
    3. Concurrent Mark
        * 전체 Region에 대해 스캔하여, GC 대상 객체가 존재하지 않는 Region은 이후 단계에서 제외됩니다.
    4. Remark
        * Stop-The-World 후, GC 대상에서 제외할 객체를 식별합니다.
    5. Cleanup
        * Stop-The-World 후, 살아있는 객체가 가장 적은 Region에 대해서 참조되지 않는 객체를 제거합니다.
        * Stop-The-World 끝내고 완전히 비워진 Region을 Freelist에 추가하여 재사용합니다. 
    6. Copy
        * Root Region Scan 단계에서 찾은 GC 대상 Region이었지만 Cleanup 단계에서 살아남은 객체들을 Available/Unused Region에 복사하여 Compaction 작업을 수행합니다. 
    
    <br>
        
    ![image](https://user-images.githubusercontent.com/63777714/137594932-e085179c-dd85-4568-947c-71f273ed92bc.png)


* 다음과 같은 특성 중 하나 이상이 있는 경우 G1으로 전환하는 것이 좋습니다.
    * 전체 GC 기간이 너무 길거나 너무 자주 발생합니다.
    * 객체 할당 비율 또는 승격 비율은 크게 다릅니다.
    * 원하지 않는 긴 가비지 수집 또는 압축 일시 중지(0.5~1초 이상)

    <br>
    
* * * 
References <br>
* https://velog.io/@hygoogi/%EC%9E%90%EB%B0%94-GC%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C
* https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/G1GettingStarted/index.html
