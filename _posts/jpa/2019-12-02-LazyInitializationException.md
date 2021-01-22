---
title: 'LazyInitializationException'
layout: post
categories: jpa
---

> failed to lazily initialize a collection of role: could not initialize proxy - no Session

## 원인
- lazy 로딩을 할때 2단계를 거친다
  - 1단계: main object를 가져온다
  - 2단계:  main object의 프록시에서 데이터를 구한다
- 두 단계가 하나의 오픈 세션(동일한 트랜잭션)에서 실행되어야 한다
- 트랜잭션이 닫힌 후 두번째 단계가 실행되면 LazyInitializationException이 발생한다

## 해결책
- lazy로딩을 하는 서비스 로직에 @Transactional 추가
- spring.jpa.properties.hibernate.enable_lazy_load_no_trans=true
  - 느림
  - N+1 이슈 있음



