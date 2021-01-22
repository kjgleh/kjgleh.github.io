---
title: '자바에서 코틀린 호출'
layout: post
categories: kotlin
---

## 프로퍼티
- 코틀린 프로퍼티는 다음과 같이 컴파일된다

코틀린
```kotlin
var firstName: String
```
자바
```java
private String firstName;

public String getFirstName() {
    return firstName;
}

public void setFirstName(String firstName) {
    this.firstName = firstName;
}
```
- 프로퍼티명이 is로 시작하면 아래의 규칙에 따른다
    - getter는 프로퍼티명 그대로 사용한다
    - setter는 is를 set으로 바꾼다
    - 예) 프로퍼티명이 isOpen이면 getter는 isOpen()이고 setter는 setOpen()이다

## 패키지 레벨 함수
- 확장 함수를 포함하여 패키지 org.example 내의 app.kt 파일에 선언 된 모든 함수와 속성은 org.example.AppKt라는 Java 클래스의 정적 메서드로 컴파일된다

코틀린
```kotlin
// app.kt
package org.example

class Util

fun getTime() {}
```
자바에서 호출할 때
```java
new org.example.Util();
org.example.AppKt.getTime();
```
- 생성 된 Java 클래스의 이름은 @JvmName 주석을 사용하여 변경할 수 있다

코틀린
```kotlin
@file:JvmName("DemoUtils")

package org.example

class Util

fun getTime() { /*...*/ }
```
자바에서 호출
```java
new org.example.Util();
org.example.DemoUtils.getTime();
```

## 인스턴스 필드
- Kotlin 프로퍼티를 자바의 필드로 노출해야하는 경우 @JvmField를 사용한다

코틀린
```kotlin
class User(id: String) {
    @JvmField val ID = id
}
```
자바에서 호출
```java
class JavaClient {
    public String getID(User user) {
        return user.ID;
    }
}
```

## 정적 필드
- object 또는 companion object에 선언된 필드 중 아래 3가지 경우 자바의 static으로 컴파일된다
    - @JvmField 애노테이션이 붙은 경우
    - lateinit 변경자가 붙은 경우
    - const 변경자가 붙은 경우(최상위 레벨도 포함)

#### @JvmField 애노테이션이 붙은 경우
코틀린
```kotlin
class Key(val value: Int) {
    companion object {
        @JvmField
        val COMPARATOR: Comparator<Key> = compareBy<Key> { it.value }
    }
}
```            
자바에서 호출
```java
Key.COMPARATOR.compare(key1, key2);
// public static final field in Key class
```

#### lateinit 변경자가 붙은 경우
코틀린
```kotlin
object Singleton {
    lateinit var provider: Provider
}
```
자바에서 호출
```java
Singleton.provider = new Provider();
// public static non-final field in Singleton class
```

#### const 변경자가 붙은 경우
코틀린
```kotlin
// file example.kt

object Obj {
    const val CONST = 1
}

class C {
    companion object {
        const val VERSION = 9
    }
}

const val MAX = 239
```
자바에서 호출
```java
int const = Obj.CONST;
int version = C.VERSION;
int max = ExampleKt.MAX;
```

## 정적 메소드
- 패키지 수준 함수
- named object 나 companion object 정의된 메소드에 @JvmStatic 애노테이션이 추가된 함수

코틀린
```kotlin
class C {
    
    companion object {
        @JvmStatic 
        fun callStatic() {}
        
        fun callNonStatic() {}
    }
}
```
자바에서 호출
```java
C.callStatic(); // works fine
C.callNonStatic(); // error: not a static method
C.Companion.callStatic(); // instance method remains
C.Companion.callNonStatic(); // the only way it works
```

코틀린
```kotlin
object Obj {

    @JvmStatic 
    fun callStatic() {}
    
    fun callNonStatic() {}
}
```
자바에서 호출
```java
Obj.callStatic(); // works fine
Obj.callNonStatic(); // error
Obj.INSTANCE.callNonStatic(); // works, a call through the singleton instance
Obj.INSTANCE.callStatic(); // works too
```
## Reference
- <https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html>
- <https://www.androidhuman.com/2016-07-10-kotlin_companion_object>