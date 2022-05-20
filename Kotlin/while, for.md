> 해당 글은 kotlin in action 을 참고하여 작성하였습니다. 

# **while과 for**
* 코틀린 while은 자바와 동일하다.
* 코틀린은 `for-each` 에 해당하는 형태만 존재한다.
* `for <아이템> in <원소들>` 형태를 가진다.

## **while**

```kotlin
while (조건) {
    /*...*/     // 조건이 참인 동안 본문을 반복 실행한다.
}

do {
    /*...*/
} while (조건)  // 맨 처음에 무조건 본문을 한 번 실행한 다음, 조건이 참인 동안 본문을 반복
```

## **for**
* 코틀린에서는 `범위`를 사용한다.
    * 범위는 두 값으로 이뤄진 구간으로 `..` 연산자로 시작값과 끝 값을 연결하여 만든다.
    * ex) `val oneToTen = 1..10`
* 코틀린의 범위는 양끝을 포함하는 구간이다. 
* 범위에 속한 값을 일정한 순서로 이터레이션 하는 경우를 `수열`이라 한다.

예제로 살펴보자
```kotlin
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz "
    i % 5 == 0 -> "FizzBuzz "
    i % 3 == 0 -> "FizzBuzz "
    else -> "$i "
}

fun main(args:Array<String>) {
    // 1부터 100까지
    for (i in 1..100) {
        print(fizzBuzz(i))
    }

    // 100부터 1까지 짝수만
    for (i in 100 downTo 1 step 2) {
        print(fizzBuzz(i))
    }
}

```
* `step`을 붙이면 step 뒤에 오는 값으로 절대값이 변한다. 
* 끝값을 포함하고싶지 않다면 `until` 함수를 사용하면 된다.
    * ex) `(x in 0 until size)` 와 `(x in 0..size-1)`은 같다.

## **맵에 대한 이터레이션**

```kotlin
val binaryReps = TreeMap<Char, String>()    //  키에 대해 정렬을 위해 TreeMap을 사용

for (c in 'A'..'F') {   // 'A' 부터 'F' 까지 문자의 범위를 사용하여 이터레이션한다.
    val binary = Integer.toBinaryString(c.code) 
    binaryReps[c] = binary  // c를 키로 c의 2진 표현을 맵에 넣는다.
}

for ((letter, binary) in binaryReps) {  // 맵에 대해 이터레이션한다. 맵의 키와 값을 두 변수에 각각 대입
    println("$letter = $binary")
}

/*
    A = 1000001
    B = 1000010
    C = 1000011
    D = 1000100
    E = 1000101
    F = 1000110
*/

```
* 자바에서의 `get`과 `put`을, 코틀린에서는 `map[key]`나 `map[key] = value`로 값을 가져오고 설정할 수 있다.
* 즉, `binaryReps[c] = binary`는 `binaryReps.put(c, binary)`와 동일하다.
* 컬렉션이나 범위, 어떤 값이 범위나 컬렉션에 들어있는지 알고싶을때 in 키워드을 사용한다

예제는 다음과 같다.
## **`in`으로 컬렉션이나 범위의 원소 검사**
* `in`연산자를 사용하여 어떤 값이 범위에 속하는지 검사할 수 있다. 
* `!in`을 사용하면 어떤 값이 범위에 속하지 않는지를 검사할 수 있다. 
```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c:Char) = c !in '0'..'9'

fun main(args:Array<String>) {
    println(isLetter('q')) // true
    println(isNotDigit('x'))    // true
}
```

다음과 같이 in을 when에서도 활용 가능하다.
```kotlin
// when에서 in 사용하기
fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know"
}

fun main(args:Array<String>) {
    println(recognize('8')) // It's a digit!
}
```
