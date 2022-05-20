> 해당 글은 kotlin in action 을 참고하여 작성하였습니다. 

# **enum과 when**

## **enum**
enum 은 다음과 같이 사용한다. 

```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```

위의 예시는 가장 기본인 형태로 나타낸 enum 클래스이며, 다음과 같이 프로퍼티나 메소드를 정의할 수도 있다. 

```kotlin
enum class Color(var r: Int, val g: Int, val b: Int) {

    RED(255, 0,0), ORANGE(255,165,0), YELLOW(255,255,0),
    GREEN(0,255,0), BLUE(0,0,255),
    INDIGI(75,0,130), VIOLET(238, 130, 238);


    fun rgb() = (r * 256 + g) * 256 + b
}

fun main(args: Array<String>) {
    println(Color.BLUE.rgb())    // 255

}
```
중요한 것은 코틀린에서 원래는 `세미콜론(;)`을 사용하지 않지만 **enum 클래스 안에서 메스드를 정의하는 경우 반드시 enum 상수목록과 메서드 정의 사이에 `세미콜론(;)`을 넣어야 한다.**

## **when에 enum 적용시키기**
```kotlin
fun getMnemonic(color: Color) =
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }


fun main(args: Array<String>) {
    println(getMnemonic(Color.BLUE)) // Battle
}
```
`when`은 자바에서 `switch`에 해당하는 함수라고 볼 수 있다. 자바와 다른 점으로는 `break` 를 넣지 않아도 된다는 점이다. 이로 인해 `break`를 적지않아 발생하는 오류를 방지할 수 있다. 

또한 하나의 when 분기 안에서 여러 값을 사용할 때에는 `콤마(,)`를 사용하면 된다. 
```kotlin
fun getWarmth(color: Color) =
        when (color) {
            Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
            Color.GREEN -> "neutral"
            Color.BLUE, Color.VIOLET, Color.INDIGO -> "cold"
        }
```

## **when 분기 조건에 다른 객체 사용하기**
```kotlin
fun mix(c1: Color, c2: Color) =
        when (setOf(c1, c2)) {
            setOf(Color.RED, Color.YELLOW) -> Color.ORANGE
            setOf(Color.YELLOW, Color.BLUE) -> Color.GREEN
            setOf(Color.BLUE, Color.VIOLET) -> Color.INDIGO
            else -> throw Exception("Dirty color")

        }

fun main(args: Array<String>) {

    println(mix(Color.RED, Color.YELLOW)) // ORANGE
}
```
위 예제에선 인자로 전달받은 여러 객체를 그 객체들을 포함하는 집합인 Set 객체로 만드는 `setOf` 함수를 사용했다. 인자의 순서는 관계없다. (자바로는 `HashSet` 과 비슷하다.)

그리고 나머지는 `else` 를 사용하여 처리할수도 있다. 

## **인자가 없는 when**
이전 예제와는 달리 when에 인자값을 넣지 않고도 사용할 수 있다. 다만, **<u>when에 인자값을 넣지 않으려면 각 분기의 조건이 boolean 결과를 계산하는 expression이어야 한다.</u>**
```kotlin
// 인자가 없는 when 사용
fun mixOptimized(c1: Color, c2: Color) =
    when {
        (c1 == Color.RED && c2 == Color.YELLOW) || (c1 == Color.YELLOW && c2 == Color.RED) ->
            Color.ORANGE
        (c1 == Color.YELLOW && c2 == Color.BLUE) || (c1 == Color.BLUE && c2 == Color.YELLOW) ->
            Color.GREEN
        (c1 == Color.BLUE && c2 == Color.VIOLET) || (c1 == Color.VIOLET && c2 == Color.BLUE) ->
            Color.INDIGO
        else -> throw Exception("Dirty color")
    }


fun main(args: Array<String>) {

    println(mixOptimized(Color.YELLOW, Color.RED)) // ORANGE
}
```

하지만, 인자를 빼면 가독성이 좋지 않다. 

## **스마트 캐스트(smart cast)**
* 코틀린에서는 `is`를 사용하여 변수 타입을 검사한다.
    * 자바의 `instanceof` 와 유사
* 코틀린에서 `as`로 타입을 캐스팅할 수 있다. 
* 코틀린은 `is`로 원하는 타입을 검사한 뒤면 따로 캐스팅을 선언하지 않아도 된다.
    * 컴파일러가 캐스팅을 수행해준다.
* 이를 `smart cast` 라고 한다.
```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right:Expr) : Expr

fun eval(e: Expr):Int {
    if (e is Num) { // smart cast 가 발생
        val n = e as Num    // 윗 라인에서 is로 검사를 수행했기 때문에 이미 Num 타입으로 전환 (생략가능)
        return n.value
    }

    if (e is Sum) { // smart cast 가 발생
        // 따라서 아래 라인에서 Sum의 프로퍼티인 right와 left로 접근 가능
        return eval(e.right) + eval(e.left)
    }
    throw IllegalArgumentException("Unknown expression")

}

fun main(args: Array<String>) {
    println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))

}
```

위의 코드를 when을 사용하여 리팩토링 해보자.
```kotlin
fun eval2(e: Expr): Int =
        when (e) {
            is Num -> e.value 
            is Sum -> eval2(e.right) + eval2(e.left)
            else -> throw IllegalArgumentException("Unknown expression")
        }

```

훨씬 간결해졌다. 

## **if와 when의 분기에서 블록 사용**
* if와 when 분기에 블록을 사용할 수 있다.
* 이 경우 블록의 마지막 문장이 블록 전체의 결과가 된다. 
   
```kotlin

fun evalWithLogging(e: Expr): Int =
        when (e) {
            is Num -> {
                println("num: ${e.value}")
                e.value
            }
            is Sum -> {
                val left = evalWithLogging(e.left)
                val right = evalWithLogging(e.right)
                println("sum: $left + $right")
                left + right
            }
            else -> throw IllegalArgumentException("Unknown expression")

        }

fun main(args: Array<String>) {
    println(evalWithLogging(Sum(Sum(Num(1), Num(2)), Num(4))))
    // num: 1
    // num: 2
    // sum: 1 + 2
    // num: 4
    // sum: 3 + 4
    // 7
}
```
    