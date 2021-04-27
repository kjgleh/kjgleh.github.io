---
title: 'Mockito Strict Stubbing and The UnnecessaryStubbingException'
layout: post
categories: spring
---

## Strict Stubbing
Mockito 2.x 버전 이후로 다음의 사유로 strictness 개념이 등장한다.

- 테스트 코드에서 사용하지 않는 스텁을 감지한다.
- 중복되고 불필요한 테스트 코드를 줄인다.
- 효율적이고 정확한 디버깅을 할 수 있고 생산성을 증가시킨다.

Mockito의 기본 stubbing전략은 strict이다.

## UnnecessaryStubbingException 
테스트 실행 중에 실현되지 않은 스텁 메서드가 있는 경우 UnnecessaryStubbingException이 발생한다.
```java
@Test
public void givenUnusedStub_whenInvokingGetThenThrowUnnecessaryStubbingException() {
    when(mockList.add("one")).thenReturn(true); // this won't get called
    when(mockList.get(anyInt())).thenReturn("hello");
    assertEquals("List should contain hello", "hello", mockList.get(1));
}
```

## Bypassing Strict Stubbing
lenient()을 사용하여 우회할수 있다.
```java
@Test
public void givenLenientdStub_whenInvokingGetThenThrowUnnecessaryStubbingException() {
    lenient().when(mockList.add("one")).thenReturn(true);
    when(mockList.get(anyInt())).thenReturn("hello");
    assertEquals("List should contain hello", "hello", mockList.get(1));
}
```

## Reference
- <https://www.baeldung.com/mockito-unnecessary-stubbing-exception>




