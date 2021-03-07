---
title: 'Constructor'
layout: post
categories: kotlin
---

## 생성자
주 생성자와 보조 생성자로 나눌 수 있다.

## 주 생성자
- 클래스 선언 부분에 작성한다.
- 하나의 클래스에 하나의 주 생성자만 정의 가능하다.
- 보조 생성자가 있으면 작성하지 않을 수 있다.

#### 선언 방법
```kotlin
// 주 생성자, 보조 생성자 둘 다 없을 경우 컴파일러가 자동으로 매개변수가 없는 주 생성자를 추가한다.
class MyClass1 {}

class MyClass2() {}

// constructor에 별도의 수식구문(어노테이션, 접근 제한자 등)이 없다면 위처럼 constructor 예약어를 생략할 수 있다.
class MyClass3 constructor() {}

// 매개변수가 있는 주생성자를 선언할 수 있다.
class MyClass4(param1: String, param2: String)

// 생성자가 있으면 매개변수가 없는 주 생성자가 자동으로 추가되지 않으므로 아래는 에러가 발생한다.
MyClass4()
```

## init
> 주 생성자의 실행 구문을 작성할 수 있다.

객체가 생성될 때 클래스 내에 init예약어로 선언한 초기화 블록도 함께 실행된다. 
```kotlin
class MyClass(param1: String, param2: String) {

    init {
        println("param1: $param1, param2: $param2")
    }
}
```

## 보조 생성자
클래스 몸체에 constructor 예약어로 선언한다.
```kotlin
// 컴파일러가 매개변수가 없는 주 생성자 자동 추가
class MyClass1 {}

// 주 생성자만 추가
class MyClass2(param1: String) { }

// 보조 생성자만 추가
class MyClass3 {
    
    constructor(param1: String) {}
    constructor(param1: String, param2: String) {}
}
```

#### 주 생성자를 선언했다면 보조생성자는 무조건 주 생성자를 호출해 주어야 한다.
```kotlin
class MyClass4(param1: String) {

    constructor(param1: String, param2: String): this(param1) {}
}
```