---
title: '@ElementCollection'
layout: post
categories: jpa
---

## @ElementCollection
값 타입을 하나 이상 저장할 때 사용한다.

```java
@Entity
public class Member {
    
    @Id @GeneratedValue
    private Long id;

    @Embedded
    private Address homeAddress;

    @ElementCollection
    @CollectionTable(name = "ADDRESS", joinColumns = @JoinColumn(name = "MEMBER_ID"))
    private List<Address> addressHistory = new ArrayList<Address>();
}
```

- @ElementCollection은 영속선 전이(Cascade) + 고아 객체 제거(ORPHAN REMOVE) 기능을 필수로 가진다고 볼 수 있다.
- @ElementCollection의 기본 Fetch 전략은 LAZY이다.

## 값 타입 컬렉션의 제약 사항
값 타입 컬렉션을 변경했을 때 JPA 구현체들은 테이블의 기본 키를 식별해서 변경된 내용만 반영하려고 노력한다.  
하지만 사용하는 컬렉션이나 여러 조건에 따라 기본 키를 식별할 수도 있고 식별하지 못할 수도 있다.  
따라서 값 타입 컬렉션을 사용할 때는 모두 삭제하고 다시 저장하는 최악의 시나리오를 고려하면서 사용해야 한다.
