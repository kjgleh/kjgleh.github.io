---
title: 'Spring Security의 인증 과정'
layout: post
categories: spring
---

## Spring Security의 인증 과정
![](/asset/images/spring/spring-security_01.PNG)

1. 서버에 로그인 인증 요청을 하면 Spring Security에 Chain형태로 구성된 Filter들이 동작한다.
    - UsernamePasswordAuthenticationFilter가 인증처리를 진행한다.
2. UsernamePasswordAuthenticationFilter는 인증되지 않은 Authentication 객체를 생성한다.
3. UsernamePasswordAuthenticationFilter는 생성된 Authentication 객체를 가지고 AuthenticationManager에게 인증을 요청한다.
4. AuthenticationManager는 AuthenticationProvider를 통해 인증을 시도한다.
    - 아래와 같은 AuthenticationProvider들을 제공한다.
        - CasAuthenticationProvider
        - JaasAuthenticationProvider
        - DaoAuthenticationProvider
        - OpenIDAuthenticationProvider
        - RememberMeAuthenticationProvider
        - LdapAuthenticationProvider
5. AuthenticationProvider는 UserDetailsService에게 UserDetails 정보를 요청한다.
6. UserDetailsService는 username에 해당하는 UserDetails를 생성한다.
7. UserDetailsService는 UserDetails 정보를 반환한다.
8. AuthenticationProvider는 UserDetails 정보를 가지고 인증된 Authentication 객체를 반환한다.
    - 인증에 실패하면 AuthenticationException이 발생한다.
9. AuthenticationManager는 Authentication 객체를 반환한다.
10. SecurityContext에 Authentication 객체를 저장한다.

> UsernamePasswordAuthenticationToken는 Authentication를 구현한 AbstractAuthenticationToken의 하위클래스 중 하나이며 일반적인 인증 시 Authentication의 구현체로 사용된다.

> 위 이미지에서 User는 Spring Security에서 제공하는 UserDetails의 구현 클래스이다.

## Reference
- <https://springbootdev.com/2017/08/23/spring-security-authentication-architecture/>