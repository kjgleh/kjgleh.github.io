---
title: 'Gradle basics'
layout: post
categories: gradle
---

## plugins
> Gradle은 대부분의 유용한 기능을 플러그인으로 제공한다.

```groovy
plugins {
    id «plugin id»                                            
    id «plugin id» version «plugin version» [apply «false»]   
}
```
- apply 옵션
    - 플러그인의 활성화 여부를 결졍한다.
    - 기본값은 true이다.
    - 예를 들어 subprojects에만 플러그인을 활성화하고 싶을 때 유용하게 쓰일 수 있다.

## Dependency Management Plugin
> 멀티 모듈에서 일관성있게 의존성 관리를 할 수 있다.

플러그인 적용
```groovy
plugins {
    id "io.spring.dependency-management" version <<version>>
}
```
프로젝트의 의존성의 버전을 제어한다.
```groovy
dependencyManagement {
    dependencies {
        dependency 'org.springframework:spring-core:4.0.3.RELEASE'
    }
}
```
특정 의존성을 제외시킨다.
```groovy
dependencyManagement {
    dependencies {
        dependency('org.springframework:spring-core:4.0.3.RELEASE') {
            exclude 'commons-logging:commons-logging'
        }
    }
}
``` 
dependencyManagement를 설정하면 의존성 주입 시 버전이나 제외(exclude)없이 의존성을 주입할 수 있다. 
```groovy
dependencies {
    implementation 'org.springframework:spring-core'
}
```

## dependencies
- <https://docs.gradle.org/current/userguide/java_library_plugin.html#sec:java_library_configurations_graph>

## tasks
gradle의 기본 task들 확인
```
gradlew tasks
```
- build: 해당 프로젝트만 빌드한다.
- buildNeeded: 해당 프로젝트와 해당 프로젝트를 의존하는 모든 프로젝트를 빌드한다. 
- tasks.withType(TaskType) { ... }: 특정 태스크 타입에 대해 설정을 수행한다.

```groovy
tasks.withType<Test> {
    useJUnitPlatform()
}
```

## Reference
- <https://docs.gradle.org/current/userguide/userguide.html>
- <https://docs.spring.io/dependency-management-plugin/docs/current/reference/html/>
- <https://jahyun-dev.github.io/posts/gradle-1/>
- <https://tomgregory.com/how-to-use-gradle-api-vs-implementation-dependencies-with-the-java-library-plugin/>
- <https://sikeeoh.github.io/2017/08/28/implementation-vs-api-android-gradle-plugin-3/>



