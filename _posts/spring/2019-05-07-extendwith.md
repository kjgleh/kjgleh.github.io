---
title: '@ExtendWith'
layout: post
categories: spring
---

## @ExtendWith
- Junit5에서 테스트 클래스나 메소드의 동작을 확장하기 위해 사용한다
- 예)Mock과 같은 기능을 사용하기 위해

## 사용법
```java
@ExtendWith(SpringExtension.class)
```

> @SpringBootTest에는 @ExtendWith(SpringExtension.class)가 기본 설정이라서 추가할 필요가 없다

## Reference
- <https://junit.org/junit5/docs/current/user-guide/#extensions>
- <https://stackoverflow.com/questions/61433806/junit-5-with-spring-boot-when-to-use-extendwith-spring-or-mockito>




