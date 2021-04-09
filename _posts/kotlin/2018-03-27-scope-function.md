---
title: '표준 함수'
layout: post
categories: kotlin
---

코틀린의 표준함수는 내부적으로 확장 함수이며 확장 함수를 실행하는 주체를 수신자 또는 수신자 객체라고 한다.

## apply
- 수신자 객체를 구성하기 위해 apply의 람다에 포함된 수신자 함수들을 연속적으로 호출할 수 있다.
- 람다의 실행이 끝나면 구성된 수신자 객체가 반환된다.

```kotlin
val menuFile = File("menu.txt")
menuFile.setReadable(true)
menuFile.setWriteable(true)

// apply 사용
val menuFile = File("menu.txt").apply {
    setReadable(true) // 암시적으로 menuFile.setReadable(true)가 호출된다
    setWriteable(true)
}
```

## let
인자로 전달된 람다를 실행한 후 결과를 반환해 준다.
```kotlin
val firstItemSquared = listOf(1, 2, 3).first().let { it * it }
```

## apply와 let의 차이점
- let은 수신자 객체를 람다로 전달하지만 apply는 아무것도 전달하지 않는다.  
- let은 람다의 실행 결과를, apply는 현재의 수신자 객체를 반환한다.

## run
- apply와 동일한 연관 범위를 제공한다. 
- 람다의 결과를 반환한다.

```kotlin
val menuFile = File("menu.txt")
val isTest = menuFile.run {
    readText().contains("test")
} 
```

함수 호출이 여러 개 있을 때 run을 사용하면 편리하고 가독성이 좋아진다. 
```kotlin
fun nameIsLong(name: String) = name.length >= 20
fun createMessage(nameTooLong: Boolean): String {
    return if (nameTooLong) {
        "Name is too long. Please choose another name."
    } else {
        "Welcome"
    }
}

// 일반적인 구현
println(createMessage(nameIsLong("my name")))

// run 사용
"my name"
    .run(::nameIsLong)
    .run(::createMessage)
    .run(::println)
```

## With
- run과 동일하지만 호출 방식이 다르다. with는 수신자 객체를 첫 번째 매개변수의 인자로 받는다.
- 다른 표준 함수들과 일관성이 없으므로 대신 run을 사용할 것을 권한다.

```kotlin
val nameIsLong = with("my name") {
    length >= 20
}
```

## also
- let과 유사하게 동작한다.
- let과 다르게 수신자 객체를 반환한다.

수신자 객체를 반환하기 때문에 서로 다른 처리를 also를 사용해 연쇄 호출할 수 있다.
```kotlin
val fileContents: List<String>
File("file.txt")
    .also { println(it.name) }
    .alse { fileContents = it.readLine() }
```

## takeIf
람다에 제공된 조건식을 실행한 후 그 결과에 따라 true이면 수신자 객체를 false이면 null를 반환한다.

```kotlin
// takeIf를 사용하지 않는 경우
val file = File("file.txt")
val fileContents = if (file.canRead() && file.canWrite()) {
    file.readText()
} else {
    null
}

// takeIf를 사용
val fileContents = File("myfile.txt")
    .takeIf { it.canRead() && it.canWrite() }
    ?.readText()
```

## 요약

| 함수 | 수신자 객체를 람다의 인자로 전달하는가? | 연관 범위를 제공하는가? | 반환 |
| --- | --- | --- | --- |
| let | Yes | No | 람다의 결과 |
| apply | No | Yes | 수신자 객체 |
| run | No | Yes | 람다의 결과 |
| with | No | Yes | 람다의 결과 |
| also | Yes | No | 수신자 객체 |
| takeIf | Yes | No | 수신자 객체의 null 가능 버전 |

## Reference
- [빅 너드 랜치의 코틀린 프로그래밍](http://www.yes24.com/Product/Goods/70968413?OzSrank=17)
