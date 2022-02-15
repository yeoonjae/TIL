# **Data Structure (자료구조) - 작성중**

## **자료구조란?**
* 자료구조란 프로그램에서 사용할 많은 데이터를 메모리 상에서 관리하는 여러 구현 방법들
* 효율적인 자료구조가 성능 좋은 알고리즘의 기반이 됨
* 자료의 효율적인 관리는 프로그램의 수행속도와 밀접한 관련이 있음
* 여러 자료 구조 중에서 구현하려는 프로그램에 맞는 최적의 자료구조를 활용해야 하므로 자료구조에 대한 이해 중요.

## **자료구조 종류**

![image](https://user-images.githubusercontent.com/63777714/154024564-ebda53d6-6f20-4d73-bc47-5a4769359bf6.png)

자료구조는 크게 `Map`, `Set`, `List` 세가지로 나눌 수 있습니다. 그 중 `List`, `Set`의 공통된 부분을 뽑아 `Collection` 이라는 인터페이스로 정의하였습니다. 

* `List`: ArrayList, LinkedList, Vector, Stack
* `Set`: HashSet, LinkedHashSet, TreeSet
* `Queue`: priorityQueue, ArrayDeque
* `Map`: HashMap, LinkedHashMap, Hashtable, TreeMap
 
 |인터페이스 | 특징|
 |--|--|
 |`List` |순서가 있는 데이터의 집합. 데이터의 중복을 허용|
 |`Set` |순서를 유지하지 않는 데이터의 집합. 데이터의 중복을 허용하지 않는다. |
 |`Map` |Key와 Value 값의 쌍으로 이루어진 데이터의 집합. 순서는 유지되지 않으며, 키는 중복을 허용하지 않고, 값은 중복을 허용한다. |

### **List 인터페이스**
* 중복 허용 , 저장순서 유지
* `Vector`, `ArrayList`, `LinkedList`

|Method|설명|
|--|--|
|`void add(int index, Object element)`<br>`boolean addAll(int index, Collection c)`|지정된 위치(index)에 객체(element) 또는 컬렉션에 포함된 객체들을 추가한다. |
|`Object get(int index)`|지정된 위치(index)에 있는 객체를 반환한다. (순방향)|
|`int lastIndexOf(Object o)`|지정된 객체의 위치(index)를 반환한다. (역방향) |
|`Listliterator listiteratir()`<br>`Listliterator listiteratir(int index)`|List 의 객체에 접근할 수 있는 Listliterator를 반환한다.|
|`Object remove(int index)`|지정된 위치에 있는 객체를 삭제하고 삭제된 객체를 반환한다. |
|`Object set(int index, Object element)`|지정된 위치(index)에 객체(element)를 저장한다. |
|`void sort(Comparator c)`|지정된 비교자로 List를 정렬한다.|
|`List subList(int fromIndex, int toIndex)`|지정된 범위에 있는 객체를 반환한다. |

### **Set 인터페이스**
* 중복을 허용하지 않음, 저장 순서가 유지되지 않음.
* `HashSet`, `TreeSet`

### **Map 인터페이스**
* key-value 쌍으로 묶어서 저장하는 컬렉션.
* 키는 중복이 불가능, 값은 중복이 가능
* 키가 중복될 경우 기존의 값이 없어지고, 마지막에 저장된 값이 남게 된다. 
* `HashTable`, `HashNap`, `LinkedHashMap`, `SortedMap`, `TreeMap`

|Method|설명|
|--|--|
|`void clear()`|Map의 모든 객체를 삭제한다.|
|`boolean containsKey(Object key)`|지정된 key 객체와 일치하는 Map의 key 객체가 있는지 확인한다.|
|`boolean containsValue(Object value)`|지정된 value 객체와 일치하는 Map의 value 객체가 있는지 확인한다.|
|`Set entrySer()`|Map에 저장되어있는 key-value 쌍을 `Map.Entry` 타입의 객체로 저장한 Set으로 반환한다.|
|`boolean equals(Object o)`|동일한 Map 인지 비교한다.|
|`Object get(Object key)`|지정한 key 객체에 대응하는 value 객체를 찾아서 반환한다.|
|`int hashcode()`|해시코드를 반환한다.|
|`Object put(Object key, Object value)`|Map에 key-value 쌍을 추가한다.|
|`void putAll(Map t)`|지정된 Map의 모든 key-value 쌍을 추가한다.|
|`Object remove(Object key)`|지정된 key 객체와 일치하는 key-value 값을 삭제한다.|
|`int size()`|Map 에 지정된 key-value 쌍의 개수를 반환한다.|
|`Collection values()`|Map 저장된 모든 value 객체를 반환한다.|





## **`List`**

* `ArrayList`
    * Object 배열을 이용하여 데이터를 순차적으로 저장
    * 만든 배열에 더이상 저장할 공간이 없으면 큰 새로운 배열을 생성하여 기존의 배열을 복사한 다음 저장.
    * 실행속도를 향상시키기 위해서는 처음부터 충분히 큰 크기의 배열을 생성해야하므로 메모리가 낭비된다. 
    * 비순차적인 데이터의 추가 또는 삭제에 시간이 많이 걸린다.
        * 차례대로 데이터를 추가하고 마지막에서부터 데이터를 삭제하는 것은 빠르지만, 
        * 배열의 중간에 데이터를 추가하려면 빈자를 만들기 위해서 다른 데이터들을 복사해서 이동해야 한다. 
* `LinkedList` (연결리스트)
    * `ArrayList`의 단점을 보완하기 위해 나온 자료구조.
    * 모든 데이터가 불연속적으로 존재하는 데이터를 서로 연결(link) 한 형태
    * 각 요소(node)들은 자신과 연결된 다음 요소에 대한 참조(주소값)와 데이터로 구성
    ```java
    class Node {
        Node next;  // 다음 요소의 주소를 저장
        Object obj; // 데이터를 저장
    }
    ```
    * 데이터를 삭제할 경우 삭제하고자 하는 요소의 이전 요소가 삭제하고자 하는 요소의 다음 요소를 참조하도록 변경하면 됨
    * ArrayList처럼 복사하는 과정이 필요하지 않기에 처리속도가 빠르다. 
    
> 🔍 **순차적으로 추가/삭제하는 경우엔 ArrayList가 LinkedList 보다 빠르다.** <br>
> 순자척 삭제란 뒤에서부터 삭제한다는 것을 의미하는 것으로, ArrayList는 뒤에서부터 차례대로 삭제할 경우 복사를 하지 않고, null로 표기하면 되기 때문에 상당히 빠르다. 

> 🔍 **중간 데이터를 추가/삭제하는 경우에는 LinkedList가 ArrayList보다 빠르다.** <br>
> 중간 요소를 변경하는 경우 LinkedList 는 요소간의 연결만 변경해주면 되기 때문에 속도가 빠르다. 

> 🔍 **ArrayList vs LinkedList** <br>
> 
> |컬렉션|읽기(접근시간)|추가 / 삭제| 기타 |
> |--|--|--|--|
> |ArrayList|빠르다.|느리다.|순차적인 추가삭제는 더 빠름 / 비효율적인 메모리 사용|
> |LinkedList|느리다.|빠르다.|데이터가 많을수록 접근성이 떨어짐|

* `Stack`
    * 마지막에 들어간 데이터를 가장 먼저 꺼내는 LIFO(Last In First Out) 구조

    ![image](https://user-images.githubusercontent.com/63777714/154039820-ada34615-2690-4074-9068-dc7cb0535975.png)


* `Queue`
    * 먼저 들어간 데이터를 가장 먼저 꺼내는 FIFO(First In First Out) 구조

    ![image](https://user-images.githubusercontent.com/63777714/154039873-0c1390da-f4fe-4495-bf45-53e04b71d9bb.png)

> Stack Queue 의 자료구조 선택
> 
> 순차적으로 데이터를 삭제하고 추가하는 Stack 에는 ArrayList 가 적합하다. <br>
> 하지만, 큐는 데이터를 삭제할 때 제일 처음 저장된 데이터를 삭제하기 때문에 LinkedList 가 적합하다.

|Method|설명|
|--|--|
|`boolean empty()`|Stack이 비어있는지 알려준다.|
|`Object peek()`|Stack 의 맨 위에 저장된 객체를 반환. pop()과 달리 Stack에서 객체를 꺼내지는 않음.|
|`Object pop()`|Stack 의 맨 위의 저장된 객체를 꺼낸다. |
|`Object push(Object item)`|stack에 객체를 저장한다.|
|`int search(Object o)`|Stack에서 주어진 객체를 찾아서 반환, 못찾으면 -1을 반환|


|Method|설명|
|--|--|
|`boolean add(Object o)`|지정된 객체를 Queue에 추가한다. 성공하면 true를 반환, 저장공간이 부족하면 예외 발생|
|`Object remove()`|Queue에서 객체를 꺼내 반환한다.|
|`Object element()`|삭제없이 요소를 읽어온다.|
|`Object offer(Object o)`|Queue 에 객체를 저장. 성공하면 true, 실패하면 false를 반환|
|`Object poll()`|Queue 에서 객체를 꺼내서 반환. 비어있으면 null 을 반환|
|`Object peek()`|삭제없이 요소를 읽어온다. Queue가 비어있으며 null을 반환|


> 🔍 스택과 큐의 활용 <br>
> 스택의 활용 예 : 수식계산, 수식괄호검사, 웹브라우저의 뒤로/앞으로 <br>
> 큐의 활용 예 : 최근사용문서, 버퍼

* `PriorityQueue`
    * Queue 인터페이스의 구현체 중의 하나로, 저장한 순서에 관계없이 우선순위가 높은 것부터 꺼내게 된다는 특정이 있다. 
    * 저장공간으로 배열을 사용하며, 각 요소를 `힙(heap)`이라는 자료구조의 형태로 저장한다. 
        * `힙(heap)`은 가장 큰 값이나 적은 값을 빠르게 찾을 수 있다는 특징이 있다. 
* `Deque` 
    * Queue 의 변형으로 한 쪽 끝으로만 추가/삭제할 수 있는 Queue와 달리 Deque는 양쪽 끝에 추가/삭제가 가능하다. 

## **`Set`**
* `HashSet`
    * Set 인터페이스를 구현한 가장 대표적인 컬렉션, Set 인터페이스의 특징대로 중복 요소를 허용하지 않음
    * 중복된 요소를 저장하려 한다면 false 를 반환하여 저장에 실패함을 알림.
    * HashSer의 특징을 이용하여 컬렉션 내의 중복 요소들 제거 가능
    * 저장 순서를 유지하지 않으므로 순서를 유지하고 싶다면 LinkedHashSet을 이용
    * HashSet의 add 메소드는 새로운 요소를 추가하기 전에 기존에 저장된 요소와 같은 것인지 판별하기 위해 추가하려는 요소의 equals()와 hashcode() 를 호출하기 때문에 **equals()와 hashcode()를 오버라이딩**해야 한다. 
    * hashcode()를 사용하는 컬렉션의 기능의 검색속도를 향상시키기 위해선 반드시 오버라이딩을 하는 것이 좋다. 

* `TreeSet`
    * TreeSet은 이진 검색 트리라는 자료구조의 형태로 데이터를 저장하는 컬렉션 클래스. 
    * 중복된 데이터의 저장을 허용하지 않으며, 정렬된 위치에 저장하므로 저장순서를 유지하지도 않는다. 
    * 여러 개의 노드가 서로 연결된 구조. 

    ![image](https://user-images.githubusercontent.com/63777714/154053338-1b58f23e-7cf3-4275-9ff9-418cf40c4efd.png)

    * 이진 검색 트리는 부모노드의 왼쪽에는 부모노드의 값보다 작은 값의 자식노드를, 오른쪽에는 큰 값의 자식 노드를 저장하는 이진 트리이다. 

    ![image](https://user-images.githubusercontent.com/63777714/154053660-dc0d560e-1e7d-446d-9544-5e486f60176f.png)

> 🔍 이진 검색 트리
> * 모든 노드는 최대 두 개의 자식노드를 가질 수 있다.
> * 왼쪽 자식 노드의 값은 부모노드의 값보다 작고, 오른쪽 자식노드의 값은 부모노드의 값보다 커야한다. 
> * 노드의 추가 삭제에 시간이 걸린다. (순차적으로 저장하지 않으므로)
> * 검색과 정렬에 유리하다. 
> * 중복된 값을 저장하지 못한다.


