---
title: 'JSR 305'
layout: post
categories: kotlin
---

코틀린의 주요 특성 중 하나는 Null 안정성이다. 런타임에 NullPointerException이 발생하는 대신 컴파일 타임에 null 값을 처리할 수 있다.
이는 Optional과 같은 래퍼 비용을 지불하지 않아도 된다.

Java는 타입 시스템에서는 null-safety를 표현하는 것을 허용하지 않지만 Spring Framework, SpringData 및 Reactor는 에노테이션을 통해 API의 null-safety를 제공한다.
Kotlin은 Spring API의 null-safety를 위해 JSR 305 애노테이션을 지원한다.

스프링 부트 코틀린 프로젝트를 생성하면 그래들에 다음과 같이 크틀린 컴파일 task에 옵션이 추가된다.
```groovy
tasks.withType<KotlinCompile> {
    kotlinOptions {
        freeCompilerArgs = listOf("-Xjsr305=strict")
    }
}
```

## Reference
- <https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-kotlin>
- <https://kotlinlang.org/docs/java-interop.html#null-safety-and-platform-types>




