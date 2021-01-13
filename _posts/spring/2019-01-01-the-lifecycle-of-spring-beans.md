---
title: 'The Lifecycle of Spring Beans'
layout: post
categories: spring
---

## 스프링 빈
- Spring IOC/DI container에 의해 관리되는 자바 객체
- ApplicationContext를 사용하여 빈의 생명주기를 관리

## 애플리케이션 컨텍스트
- IoC 방식을 따라 만들어진 일종의 빈 팩토리
- 별도의 정보를 참고해서 빈의 생성, 관계설정 등의 제어 작업을 총괄한다

## 애플리케이션 컨텍스트가 사용 가능한 설정 정보
- @Configuration, @Bean ...

## 애플리케이션 컨텍스트 구현
- ApplicationContext interface 구현
  - ApplicationContext interface는 BeanFactory interface를 상속했음

## 애플리케이션 컨텍스트의 장점(vs 오브젝트 팩토리)
- 클라이언트는 구체적인 팩토리 클래스를 알 필요가 없다
  - 일관된 방식으로 원하는 오브젝트를 가져올 수 있다
- 종합 IoC 서비스를 제공해준다
  - 오브젝트가 만들어지는 방식, 시점, 전략
  - 자동생성, 후처리, 인터셉팅 등 오브젝트를 효과적으로 활용
  - 빈이 사용할 수 있는 기반기술 서비스나 외부 시스템 연동 등을 컨테이너 차원에서 제공
- 빈을 검색하는 다양한 방법을 제공
  - 타입만으로 검색
  - 특별한 애노테이션 설정이 있는 빈을 검색
  
## 스프링 빈의 생명 주기
1. 빈 정의
- stereotype annotation
  - @Component
- XML bean configuration
2. 빈 생성 및 인스턴스화
- 빈이 생성되면 인스턴스화 되고 ApplicationContext와 JVM 메모리에 로드된다
3. 빈 프로퍼티 생성
- 빈 정의에 기초하여 id, scope, default values를 생성한다
4. Post-initialization
- 초기화 후 실행됨
- Aware Interface를 제공
  - 애플리케이션 빈 메타 데이터에 접근
5. 의존성 주입 및 Post-initialization이 실행되면 사용될 준비가 됨
6. Pre-distroy
- 빈이 소멸되기 전에 실행됨
7. 빈의 소멸
- JVM 메모리에서 삭제된다

## 빈의 생명주기에 후킹하기
- Spring Aware Interface 사용
- @PostConstruct, @PreDestroy 사용
  - 스프링에서 추천
- InitializingBean and DisposableBean Interfaces 사용
- @Bean annotation and Attributes 사용
- XML 설정 사용

## Reference
- <https://medium.com/swlh/the-lifecycle-of-spring-beans-b0edb8936189>
- [토비의 스프링 3.1](http://www.yes24.com/Product/Goods/7516721?OzSrank=2)




