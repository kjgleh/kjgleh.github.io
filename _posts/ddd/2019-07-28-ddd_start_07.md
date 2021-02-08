---
title: '[DDD START] 7장 도메인 서비스'
layout: post
categories: ddd
---

## 도메인 서비스
> 한 애그리거트에 넣기 애매한 도메인 개념을 구현할 때 도메인 서비스를 이용해서 구현한다.

## 도메인 서비스 구현 예
할인 금액을 계산할 때 쿠폰에 따른 할인, 회원 등급에 따른 할인이 있다.

할인 금액을 계산하는 도메인 서비스
```java
public class DiscountCalculationService {
    
    public Money calculateDiscountAmounts(
        List<OrderLine> orderLines,
        List<Coupon> coupons,
        MemberGrade grade
    ) {
        // 쿠폰 할인 금액 계산
        Money couponDiscount = 
            coupons.stream()
                    .map(coupon -> calculateDiscount(coupon))
                    .reduce(Money(0), (v1, v2) -> v1.add(v2));
        
        // 회원 등급에 따른 할인 금액 계산
        Money memberShipDiscount =
            calculateDiscount(orderer.getMember().getGrade());

        return couponDiscount.add(memberShipDiscount);                                
    }
    
    private Money calculateDiscount(Coupon coupon) {
        ...
    }
    
    private Money calculateDiscount(Coupon coupon) {
        ...
    }
}
```

Order Service
```java
public class orderService {
    private DisountCalculateService disountCalculateService;

    @Transactional
    public OrderNo placeOrder(OrderRequest orderRequest) {
        OrderNo orderNo = orderRepository.nextId();
        Order order = createOrder(orderNo, orderRequest);
        orderRepository.save(order);
        return orderNo;
    }

    private Order createOrder(OrderNo orderNo, OrderRequest orderRequest) {
        Member member = findMember(orderRequest.getOrdererId());
        Order order = new Order(...);
        order.calculateAmounts(this.disountCalculateService, member.getGrade());
        return order;
    }
}
```

Order Entity에서 DiscountCalculationService를 전달 받아 할인 금액을 계산한다.
```java
public class Order {
    
    public void calculateAmounts(
        DiscountCalculationService discountCalculationService,
        MemberGrade grade
    ) {
        Money totalAmounts = getTotalAmounts();
        Money discountAmounts = discountCalculationService.calculateDiscountAmounts(
            this.orderLines, this.coupons, grade
        );
        this.paymentAmounts = totalAmounts.minus(discountAmounts);    
    }
}
```

## 도메인 서비스의 위치
도메인 서비스는 도메인 로직을 실행하므로 도메인 서비스의 위치는 다른 도메인 구성 요소와 동일한 패키지에 위치한다.

## Reference
- [DDD START!](http://www.yes24.com/Product/Goods/27750871?OzSrank=1)