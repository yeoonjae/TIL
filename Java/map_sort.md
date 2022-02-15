# **Java 에서 값별로 맵을 정렬하는 방법 (작성중)**

## **1. Java 에서 `sort()`를 사용하여 `Map <key, value>` 정렬**
List 의 `sort()` 메소드를 사용하여 Map의 요소 정렬. `sort()` 메소드는 요소를 오름차순으로 정렬하고 `comparingByValue()` 메소드를 이용하여 값별로 정렬합니다. 
```java
  @Test
  void mapSortedTest() {
    Map<Integer, Integer> map = new HashMap<>();
    map.put(5, 500);
    map.put(1, 1200);
    map.put(2, 30);
    map.put(6, 550);
    map.put(4, 1300);
    System.out.println("정렬 전");
    map.forEach((k,v) -> System.out.println(k + "-"+ v));
    System.out.println();
    System.out.println("정렬 후");
    List<Entry<Integer, Integer>> list = new ArrayList<>(map.entrySet());
    list.sort(Entry.comparingByValue());
    list.forEach(System.out::println);
  }
```
결과
```
정렬 전
1-1200
2-30
4-1300
5-500
6-550

정렬 후
2=30
5=500
6=550
1=1200
4=1300
```

