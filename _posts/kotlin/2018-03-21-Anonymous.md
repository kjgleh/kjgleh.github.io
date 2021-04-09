---
title: '익명함수'
layout: post
categories: kotlin
---

## 용어 정리
- 익명 함수 = 람다
- 익명 함수 정의 = 람다 표현식, 람다식

## 익명 함수
- 함수 정의 부분에 이름이 없다.
- 주로 다른 함수의 인자로 전달되거나 반환되는 형태로 사용된다.

## 익명 함수의 용도

#### 특정 함수를 사용할 때 원하는 대로 실행되도록 규칙을 지정할 수 있다.
표준 라이브러리 사용 예시
```kotlin
val count = "Mississippi".count ({ letter -> 
                letter == 's' 
            })
```

## 익명 함수 사용법

#### 정의
{ 표현식이나 명령문 }
```kotlin
{
    val currentYear = 2019
    "copyright $currentYear"
}
```

#### 호출
{ 표현식이나 명령문 }()
```kotlin
{
    val currentYear = 2019
    "copyright $currentYear"
}()
```

## 익명 함수 타입
함수 타입의 변수들은 값으로 익명 함수를 저장한다. 그리고 다른 변수처럼 익명 함수가 코드 어디든 전달될수 있다.

#### 익명 함수를 변수에 저장
```kotlin
val greetingFunction: () -> String = {
    val currentYear = 2019
    "copyright $currentYear"
}
```
함수 타입이 () -> String인 익명함수이다. ()는 인자를 받지 않는다는 것을 의미하며 String은 반환값이다.  
즉 인자를 받지 않고 String을 반환하는 함수를 의미한다.

## 암시적 반환
위의 익명함수의 정의해서 보듯이 익명함수는 자동으로 마지막 코드의 결과를 반환한다.
또한 익명함수에는 return 키워드를 사용이 금지되어 있다.
익명 함수에 return 키워드가 있을 때는 어떤 곳으로 제어가 복귀되어야 하는지 컴파일러가 알 수 없기 때문이다.
 
## 함수 인자
이름이 있는 함수처럼 익명 함수는 어떤 타입의 인자도 받을 수 있다.

#### 매개변수를 익명함수에 추가하기
```kotlin
val greetingFunction: (String) -> String = { playerName ->
    val currentYear = 2019
     "hello $playerName (copyright $currentYear)"
}

println(greetingFunction("김선달"))
```
위의 count함수는 (char) -> Boolean 타입의 익명함수를 인자로 받는다.

## it 키워드
하나의 인자만 받는 익명 함수에는 매개변수 이름을 지정하는 대신 it 키워드를 사용할 수 있다.

#### 위의 greetingFunction을 it 키워드 사용으로 변경
```kotlin
val greetingFunction: (String) -> String = {
    val currentYear = 2019
     "hello $it (copyright $currentYear)"
}

println(greetingFunction("김선달"))
```

## 다수의 인자 받기
```kotlin
val greetingFunction: (String, Int) -> String = { playerName, age ->
    val currentYear = 2019
     "hello $playerName(나이 $age) (copyright $currentYear)"
}

println(greetingFunction("김선달", 2))
```

## 타입 추론 지원

#### 인자가 없는 익명 함수의 타입 추론
```kotlin
val greetingFunction: () -> String = {
    val currentYear = 2019
    "copyright $currentYear"
}
```
```kotlin
val greetingFunction = {
    val currentYear = 2019
    "copyright $currentYear"
}
```

#### 하나 이상의 인자를 받을 때 함수 타입을 생략하려면 컴파일러가 변수 타입을 추론할 수 있도록 익명 함수 정의에 각 매개변수 이름과 타입을 지정해야 한다.
```kotlin
val greetingFunction: (String, Int) -> String = { playerName, age ->
    val currentYear = 2019
     "hello $playerName(나이 $age) (copyright $currentYear)"
}

println(greetingFunction("김선달", 2))
```
```kotlin
val greetingFunction = { playerName: String, age: Int ->
    val currentYear = 2019
     "hello $playerName(나이 $age) (copyright $currentYear)"
}

println(greetingFunction("김선달", 2))
```

## 함수를 인자로 받는 함수 정의하기
```kotlin
fun main(args: Array<String>) {
    val greetingFunction = { playerName: String, age: Int ->
        val currentYear = 2019
         "hello $playerName(나이 $age) (copyright $currentYear)"
    }
    
    runSimulation("김선달", greetingFunction)
}

fun runSimulation(playerName: String, greetingFunction: (String, Int) -> String) {
    val age = (1..3).shuffled().last()
    println(greetingFunction(playerName, age))
}
```

## 단축 문법
함수에서 마지막 매개변수로 함수 타입을 받을 때는 람다 인자를 둘러싼 괄호를 생략할 수 있다.  
함수에서 마지막 매개변수로 함수 타입을 받을 때만 가능하므로 함수를 작성할 때는 함수 타입 매개변수를 마지막 매개변수로 선언하자.
```kotlin
"Mississippi".count ({ it == 's' })
```
()생략
```kotlin
"Mississippi".count { it == 's' }
```

아래 내용에도 단축문법을 적용해볼 수 있다.
```kotlin
fun main(args: Array<String>) {
    val greetingFunction = { playerName: String, age: Int ->
        val currentYear = 2019
         "hello $playerName(나이 $age) (copyright $currentYear)"
    }
    
    runSimulation("김선달", greetingFunction)
}
```
```kotlin
fun main(args: Array<String>) {
    runSimulation("김선달") { playerName: String, age: Int ->
        val currentYear = 2019
         "hello $playerName(나이 $age) (copyright $currentYear)"
    }
}
```

## 인라인 함수로 만들기
람다를 정의하면 JVM에서 객체로 생성된다. JVM은 람다를 사용하는 모든 변수의 메모리 할당을 수행하므로 메모리가 많이 사용되고 이는 성능에 영향을 줄 수 있다.  
따라서 코틀린에서는 다른 함수의 인자로 람다를 사용할 때 인라인(inline)이라는 최적화 방법을 제공한다.  

람다를 인라인 처리하려면 람다를 인자로 받는 함수에 inline 키워드를 지정하면 된다.
```kotlin
inline fun runSimulation(playerName: String, greetingFunction: (String, Int) -> String) {
    val age = (1..3).shuffled().last()
    println(greetingFunction(playerName, age))
}
```

인라인을 사용하면 코틀린 컴파일러가 바이트코드를 생성할 때 람다 코드가 포함된 runSimulation 함수 몸체의 전체 코드를 복사한 후 이 함수를 호출하는 코드에 붙여넣기 하여 교체하기 때문에
runSimulation 함수가 객체로 전달되지 않는다.

## 함수 참조
람다 외에 함수를 인자로 사용하는 방법으로 이름이 있는 함수를 인자로 전달한다.

```kotlin
inline fun runSimulation(
    playName: String,
    // 함수 참조
    consPrinter: (Int) -> Unit,
    greeting: (String, Int) -> String
) {
    val num = (1..3).shuffled().last()
    costPrinter(num)
    println(greeting(playName, num))
}

fun printConstructionCost(num: Int) {
    val cost = 500
    println("${cost * num}")
}

// runSimulation 함수 사용
fun main(args: Array<String>) {
    runSimulation("김선달", ::printConstructionCost) { playerName, num ->
        val currentYear = 2019
        "$playerName! $num $currentYear"
    }
}
```

## 반환 타입으로 함수 타입 사용하기
```kotlin
fun runSimulation() {
    val greetingFunction = configureGreetingFunction()
    println(greetingFunction("김선달"))
    // 두번째 호출
    println(greetingFunction("김선달"))
}

fun configureGreetingFunction(): (String) -> String {
    val sType = "병원"
    var numBuildings = 5
    return { playerName: String ->
        numBuildings += 1
        println("$numBuildings")
        "$playerName $sType" 
    }
}
```
configureGreetingFunction이 반환하는 람다에서 sType과 같은 지역 변수를 사용할 수 있는 이유는 코틀린의 람다가 클로저이기 때문이다.
또한 위 소스의 두번째 호출처럼 호출하면 numBuildings값이 +1이 된 상태이다. 이처럼 익명함수 외부에 정의된 변수도 익명함수에서 변경할 수 있다.

## Reference
- [빅 너드 랜치의 코틀린 프로그래밍](http://www.yes24.com/Product/Goods/70968413?OzSrank=17)
