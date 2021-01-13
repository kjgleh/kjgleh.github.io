---
title: 'data class를 jpa entity로 사용해도 되나?'
layout: post
categories: kotlin
---

## Lazy Loading
- Lazy Loading 을 지원하기 위해서 Entity Class, method, property가 final이 아니어야 함
- 코틀린은 기본적으로  모두 final
- allopen 플러그인을 사용해서 open해줌
  - open 키워드를 클래스에 붙이면 해당 클래스만 open됨
- data class도 allopen플러그인이 적용됨
- 하지만 공식문서에는 data class는 open이 되지 않는다고 나와있음

## toString()
- StackOverflowError 에러 발생

## equals() / hashCode()
- 예상과 다르게 동작할 수 있음
  - 예) ID가 DB저장시 생기면 저장 전 후의 값이 다르기 때문에 동일한 인스턴스로 인식하지 않음
