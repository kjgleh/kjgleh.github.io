---
title: 'kotlinFixture'
layout: post
categories: kotlin
---

## 의존성 추가
```groovy
testImplementation("com.appmattus.fixture:fixture:<latest-version>")
```

## 사용법
```kotlin
val fixture = kotlinFixture()

// Generate a list of strings
val aListOfStrings = fixture<List<String>>()

// Nulls are supported
val sometimesNull = fixture<Int?>()

// Create instances of classes
// Optional parameters will be randomly used or overridden
data class ADataClass(val value: String = "default")
val aClass = fixture<ADataClass>()

// Abstract classes will pick a sub-class at random
// This could be a Byte, Double, Long, Float, Int or Short
val anyNumber = fixture<Number>()

// Pick randomly from a list
val randomStringFromTheList = fixture(listOf("Cat", "Dog", "Horse"))
val anotherRandomIntFromAList = fixture(1..5)
```

## overriding configuration
```kotlin
val baseFixture = kotlinFixture {
    factory<Int> { 3 } // fixture 생성
}

val fixture = baseFixture.new {
    factory<Long> { 100L } // baseFixture의 설정을 orverriding할 수 있다.
}

println(fixture<Long>()) // Prints 100
println(fixture<Int>()) // Prints 3
println(fixture<Int> {
    factory<Int> { 4 } // (3)
}) // Prints 4
```

## repeatCount
> list나 map의 길이를 정할 수 있다.

```kotlin
val fixture = kotlinFixture {
    repeatCount { 3 }
}

val listOfThreeItems = fixture<List<Int>>() // 10, 81, 3
```

클래스 프로퍼티의 길이도 정할 수 있다.
```kotlin
class KotlinClass(val readOnly: List<String>, private var private: List<String>) {
    var member: List<String>? = null
}

val fixture = kotlinFixture {
    // Public constructor parameters overridden by reference:
    repeatCount(KotlinClass::readOnly) { 1 }

    // Private constructor parameters are overridden by name:
    repeatCount<KotlinClass>("private") { 2 }

    // Public member properties overridden by reference:
    repeatCount(KotlinClass::member) { 3 }
}
```

## subType
> 특정 자식 클래스로 인스턴스를 생성할 수 있다.

```kotlin
val fixture = kotlinFixture {
    subType<Number, Int>()
}

val alwaysInt = fixture<Number>()
```

## factory
> 특정 값을 생성할 수 있다.

```kotlin
val fixture = kotlinFixture {
    factory<Number> {
        41
    }
}

val alwaysFortyOne = fixture<Number>()

// 범위 내에서 생성할 수 있다.
factory<Number> {
    random.nextInt(10, 50)
}

// range를 사용하여 범위 내에서 생성할 수 있다.
val fixture = kotlinFixture {
    // Generate using ranges (and iterables)
    factory<Int> { range(1..10) }
}

val betweenOneAndTen = fixture<Int>()
```

## property
> 클래스의 프로퍼티에 특정 값을 지정할 수 있다.

```kotlin
class KotlinClass(val readOnly: String, private var private: String) {
    var member: String? = null
}

val fixture = kotlinFixture {
    // Public constructor parameters overridden by reference:
    property(KotlinClass::readOnly) { "a" }

    // Private constructor parameters are overridden by name:
    property<KotlinClass, String>("private") { "b" }

    // Public member properties overridden by reference:
    property(KotlinClass::member) { "c" }
}
```

## filter
```kotlin
val fixture = kotlinFixture {
    filter<Int> {
        filter { it % 2 == 0 }
    }

    // Can be used to return distinct values.
    filter<String> {
        distinct()
    }
}

val evenNumber = fixture<Int>()

val evenNumberLessThan100 = fixture<Int> {
    // Builds upon the parent configuration
    filter<Int> {
        filter { it < 100 }
    }
}
```

## nullabilityStrategy
> nullable 타입의 생성 전략을 설정할 수 있다.

```kotlin
val fixture = kotlinFixture {
    // All nullable types will be populated with a value
    nullabilityStrategy(NeverNullStrategy)
}
```
사용 가능한 전략들
- RandomlyNullStrategy(기본값): null일수도 있고 아닐 수도 있다.
- NeverNullStrategy: null이 아닌 값으로 생성한다.
- AlwaysNullStrategy: null로 생성한다.

NullabilityStrategy를 구현하여 customizing할 수 있다.

## optionalStrategy
> optional 타입의 생성 전략을 설정할 수 있다.  

```kotlin
value: String = "default"
```
```kotlin
val fixture = kotlinFixture {
    // All optionals will be populated with their default value
    optionalStrategy(AlwaysOptionalStrategy) {
        // You can override the strategy for a particular class
        classOverride<AnotherObject>(NeverOptionalStrategy)

        // You can override the strategy for a property of a class
        propertyOverride(AnotherObject::property, RandomlyOptionalStrategy)
    }
}
```
사용 가능한 전략들
- RandomlyOptionalStrategy(기본값): 기본값일 수도 있고 아닐 수도 있다.
- AlwaysOptionalStrategy: 항상 기본값으로 생성한다.
- NeverOptionalStrategy: 기본값을 사용하지 않는다.

OptionalStrategy를 구현하여 customizing할 수 있다.

## recursionStrategy
> 순환 참조 시 전략을 설정할 수 있다.

```kotlin
val fixture = kotlinFixture {
    recursionStrategy(NullRecursionStrategy)
}
```
사용 가능한 전략들
- ThrowingRecursionStrategy(기본값): 순환 참조가 발견되면 예외(UnsupportedOperationException)를 던진다.
- NullRecursionStrategy: 순환 참조를 null로 대체한다.
- UnresolvedRecursionStrategy: 순환 참조를 해결하지 않는다.

RecursionStrategy를 구현하여 customizing할 수 있다.

## constructorStrategy
> 인스턴스를 생성할 생성자를 선택할 수 있다.

```kotlin
val fixture = kotlinFixture {
    constructorStrategy(ModestConstructorStrategy)
}
```
사용 가능한 전략들
- RandomConstructorStrategy(기본값): 임의 선택
- ModestConstructorStrategy: 파리미터의 개수가 가장 적은 생성자를 선택한다.
- GreedyConstructorStrategy: 파리미터의 개수가 가장 적은 생성자를 선택한다.

ConstructorStrategy를 구현하여 customizing할 수 있다.

## Reference
- <https://github.com/appmattus/kotlinfixture>

