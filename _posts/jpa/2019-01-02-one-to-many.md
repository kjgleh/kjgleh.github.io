---
title: '일대다'
layout: post
categories: jpa
---

## 일대다 단방향 구현
팀에서 회원을 참조해야 하고 회원에서 팀을 참조할 필요가 없다면 일대다 단방향 연관 관계로 구현할 수 있다.

팀 엔티티
```kotlin
@Entity
class Team(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "TEAM_ID")
    val id: Long = 0L,

    val name: String,
   
    @OneToMany
    @JoinColumn(name = "TEAM_ID") // MEMBER 테이블의 TEAM_ID(FK)
    val members: List<Member>
)
```
@JoinColumn을 명시하지 않으면 JPA는 연결 테이블을 중간에 두고 연관관계를 관리하는 전략을 사용한다.

회원 엔티티
```kotlin
@Entity
class Member(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "MEMBER_ID")
    val id: Long = 0L,

    val username: String,
)
```

#### 일대다 단방향 매핑의 단점
- 외래 키가 다른 테이블에 있기 때문에 insert 시 외래 키가 있는 테이블을 업데이트해야 한다.
- 일대다 단방향 매핑보다는 다대일 양방향 매핑을 사용하자

## 일대다 양방향
일대다 양방향과 대다일 양방향은 같은 말이다.




