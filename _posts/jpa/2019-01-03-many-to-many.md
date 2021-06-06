---
title: '다대다'
layout: post
categories: jpa
---

## 다대다 단방향
회원 엔티티
```kotlin
@Entity
class Member(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "MEMBER_ID")
    val id: Long = 0L,

    val username: String,

    @ManyToMany
    @JoinTable(
        name = "MEMBER_PRODUCT",
        joinColumns = [JoinColumn(name = "MEMBER_ID")],
        inverseJoinColumns = [JoinColumn(name = "PRODUCT_ID")]
    )
    val products: List<Product>
)
```

상품 엔티티
```kotlin
@Entity
class Product(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "PRODUCT_ID")
    val id: Long = 0L,

    val name: String
)
```

## 다대다 양방향
회원 엔티티
```kotlin
@Entity
class Member(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "MEMBER_ID")
    val id: Long = 0L,

    val username: String,

    @ManyToMany
    @JoinTable(
        name = "MEMBER_PRODUCT",
        joinColumns = [JoinColumn(name = "MEMBER_ID")],
        inverseJoinColumns = [JoinColumn(name = "PRODUCT_ID")]
    )
    val products: List<Product>
)
```

상품 엔티티
```kotlin
@Entity
class Product(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "PRODUCT_ID")
    val id: Long = 0L,

    val name: String,

    @ManyToMany(mappedBy = "products")
    val members: List<Member>
)
```

## @ManyToMany의 한계
연결 테이블에 외래 키외에 칼럼이 필요한 경우 @ManyToMany를 사용할 수 없다.  
이런 경우 일대다, 다대일 관계로 매핑해야 한다.

회원 엔티티
```kotlin
@Entity
class Member(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "MEMBER_ID")
    val id: Long = 0L,

    val username: String,

    @OneToMany(mappedBy = "member")
    val order: Order
)
```

상품 엔티티
```kotlin
@Entity
class Product(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "PRODUCT_ID")
    val id: Long = 0L,

    val name: String
)
```

주문 엔티티
```kotlin
@Entity
class Order(

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "ORDER_ID")
    val id: Long = 0L,

    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    val member: Member,

    @ManyToOne
    @JoinColumn(name = "PRODUCT_ID")
    val product: Product,

    val orderAmount: Int
)
```

상품 엔티티에서 주문 엔티티로 탐색이 필요없다고 판단해서 단방향으로 매핑하였다.
