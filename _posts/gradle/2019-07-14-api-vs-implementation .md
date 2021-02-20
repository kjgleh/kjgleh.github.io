---
title: 'api vs implementation'
layout: post
categories: gradle
---

## api vs implementation
두가지 차이점이 있다.
- 접근성의 차이
- 컴파일 시 차이

#### 아래와 같은 디펜던시 트리가 있다고 하면
<img src="/asset/images/gradle/api-vs-implementation_01.PNG" alt="" style="width:70%;"/>

LibraryD:
```kotlin
class ClassD {

    fun tellMeAJoke():String{
        return "You are funny :D"
    }
}
```

LibraryC:
```kotlin
class ClassC {

    fun tellMeAJoke(): String {
        return "You are funny :C"
    }
}
```
LibraryB:
```kotlin
class ClassB {

    val d = ClassD()

    fun whereIsMyJoke(): String {
        return d.tellMeAJoke()
    }
}
```
LibraryA:
```kotlin

class ClassA {

    val c = ClassC()

    fun whereIsMyJoke(): String {
        return c.tellMeAJoke()
    }
}
```

## 접근성의 차이
#### api를 사용하는 경우
LibraryB의 의존성
```groovy
dependencies {
      . . . . 
      api project(path: ':libraryD')
}
```
App Module의 의존성
```groovy
dependencies {
      . . . . 
      api project(path: ':libraryB')
}
```
App Module에서 LibraryB, LibraryD 모두 접근가능하다.
```kotlin
fun getJokeFromB(): String {
    return ClassB().whereIsMyjoke()
}

fun getJokeFromD(): String {
    return ClassD().tellMeAJoke()
}
```

#### implementation을 사용하는 경우
LibraryA의 의존성
```groovy
dependencies {
      . . . . 
      implementation project(path: ':libraryC')
}
```
App Module의 의존성
```groovy
dependencies {
      . . . . 
      implementation project(path: ':libraryA')
}
```

App Module에서 LibraryA만 접근가능하다.
```kotlin
fun getJokeFromA(): String {
    return ClassA().whereIsMyjoke()
}

// LibraryC에 접근하지 못하기 때문에 컴파일 에러가 발생한다.
fun getJokeFromC(): String {
    return ClassC().tellMeAJoke()
}
```

## 컴파일의 차이
#### api를 사용할 경우
<img src="/asset/images/gradle/api-vs-implementation_02.PNG" alt="" style="width:70%;"/>
LibraryD에서 변경이 발생하면 LibraryD를 사용할 수 있는 모든 모듈(LibraryB, ModuleX)을 재컴파일해야 한다.

#### implementation를 사용할 경우
<img src="/asset/images/gradle/api-vs-implementation_03.PNG" alt="" style="width:70%;"/>
LibraryC에서 변경이 발생하면 LibraryC를 사용할 수 있는 LibraryA만 재컴파일하면 된다.

## Reference
- <https://medium.com/mindorks/implementation-vs-api-in-gradle-3-0-494c817a6fa>



