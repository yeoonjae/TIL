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
## 🔍 **CMS GC**

* Stop-the-world 시간을 최소화 하기 위해 고안된 GC
* 
