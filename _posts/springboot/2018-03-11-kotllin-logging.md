---
title: 'kotlin-logging'
layout: post
categories: springboot
---

## dependency
```kotlin
implementation("io.github.microutils:kotlin-logging:2.0.6")
또는
implementation("io.github.microutils:kotlin-logging-jvm:2.0.6")
```

## 적용 방법
```kotlin
// 방법 1: 클래스 외부에 선언
private val logger = KotlinLogging.logger {}

@Component
class SampleService {

    // 방법 2: 동반객체에 상속하도록 선언
    companion object: KLogging()

    fun hello() {
        logger.info("info...")
    }
}
```

## logback의 특정 logger를 사용하여 로깅하는 방법
logger 설정
logback-spring.xml
```xml
<configuration>
    <appender name="TEST_CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <charset>UTF-8</charset>
            <Pattern>%d %-4relative [%thread] %-5level %logger{35} - %msg%n</Pattern>
        </encoder>
    </appender>

    <logger name="MY_LOGGER" level="INFO" additivity="false">
        <appender-ref ref="TEST_CONSOLE"/>
    </logger>
</configuration>
```

logback-spring.xml에서 설정한 logger를 사용하여 logging
```kotlin
@Component
class SampleService {
    
    companion object : NamedKLogging("TEST_CONSOLE")

    fun hello() {
        logger.info("info...")
    }
}
```

## Reference
- <https://github.com/MicroUtils/kotlin-logging>
