---
title: '@Access'
layout: post
categories: jpa
---

## @Access
> JPA가 엔티티 데이터에 접근하는 방식을 지정한다

#### @Access를 설정하지 않으면 @Id의 위치를 기준으로 접근 방식이 설정된다

#### 필드 접근
- AccessType.FIELD로 지정한다
- 필드에 직접 접근한다

```java
@Entity
@Access(AccessType.FIELD)
public class Member {

    @Id
    private String id;

    private String data1;
}
```
@Id가 필드에 있으므로 @Access(AccessType.FIELD)를 생략해도 된다

#### 프로퍼티 접근
- AccessType.PROPERTY로 지정한다
- 접근자(getter)를 사용한다

```java
@Entity
@Access(AccessType.PROPERTY)
public class Member {
    
    private String id;

    private String data1;
    
    @Id
    public String getId() {
        return id;
    }

    @Column
    public String getData1() {
        return data1;
    }
}
```
@Id가 프로퍼티에 있으므로 @Access(AccessType.PROPERTY)를 생략해도 된다

#### 필드 접근 방식과 프로퍼티 접근 방식을 함께 사용할 수도 있다
```java
@Entity
public class Member {

    @Id
    private String id;

    @Transient
    private String firstName;
    
    @Transient
    private String lastName;

    @Access(AccessType.PROPERTY)
    public String getFullName() {
        return firstName + lastName;
    }
}
```

> 엔티티를 객체가 제공할 기능 중심으로 구현하도록 유도하려면 JPA 매핑 처리를 프로퍼티 방식이 아닌 필드 방식으로 선택해서 불필요한 get/set 메서드를 구현하지 말아야 한다 
