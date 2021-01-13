---
title: 'AttributeConverter'
layout: post
categories: jpa
---

## AttributeConverter
> 두 개 이상의 프로퍼티를 가진 Value 타입을 한 개 칼럼에 매핑해야 할 때 사용한다

```java
public interface AttributeConverter<X,Y> {
    public Y convertToDatabaseColumn(X attribute);
    public X convertToEntityAttribute(Y dbData);
}
```
- X는 Value 타입이고, Y는 DB 타입이다.
- convertToDatabaseColumn 메서드
    - Value 타입을 DB 칼럼으로 변환하는 기능을 구현한다
- convertToEntityAttribute 메서드
    - DB 칼럼 값을 Value로 변환하는 기능을 구현한다

## Money를 위한 AttributeConverter 구현
```java
@Converter(autoApply = true)
public class MoneyConverter implements AttributeConverter<Money, Integer> {
    
    @Override
    public Integer convertToDatabaseColumn(Money money) {
        if (money == null)
            return null;
        else
            return money.getValue();
    }
    
    @Override
    public Money convertToEntityAttribute(Integer value) {
        if (value == null) 
            return null;
        else 
            return new Money(value);
    }
}
```

## 적용
```java
@Entity
@Table(name = "purchase_order")
public class Order {
    ...
    @Column
    // @Converter(converter = MoneyConverter.class)
    private Money totalAmounts;
}
```
- MoneyConverter 클래스에서 @Converter(autoApply = true)의 autoApply = true 때문에 자동으로 적용된다
- autoApply = false가 기본 값이며 false인 경우 위 소스의 주석처럼 @Converter(converter = MoneyConverter.class)를 추가하면 된다

## Reference
- [DDD START!](http://www.yes24.com/Product/Goods/27750871?OzSrank=1)
