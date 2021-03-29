---
title: '[Design Pattern] Factory Method Pattern'
layout: post
categories: etc
---

## Factory Method Pattern
> 인스턴스 생성을 캡슐화 할 때 사용한다.  
> 인스턴스를 생성하는 Factory를 Template Method 패턴으로 구성한다.

인스턴스를 만드는 방법을 상위 클래스에서 결정하지만 인스턴스 생성은 하위 클래스에서 수행한다.

인스턴스를 만드는 방법을 제공하는 Factory 클래스
```java
public abstract class Factory {

    public final Product create(String owner) {
        Product product = createProduct(owner);
        registerProduct(product);
        return product;
    }

    // 팩토리 메서드
    protected abstract Product createProduct(String owner);
    
    protected abstract void registerProduct(Product product);
}
```

인스턴스를 생성하는 하위 클래스
```java
public class IDCardFactory extends Factory {

    private List owners = new ArrayList();
    
    // 인스턴스 생성
    protected Product createProduct(String owner) {
        return new IDCard(owner);
    }

    protected void registerProduct(Product product) {
        owners.add(((IDCard)product).getOwner());
    }
}
```

```java
public abstract class Product {
    public abstract void use();
}
```

```java
public class IDCard extends Product {
    
    private String owner;

    IDCard(String owner) {
        System.out.println(owner + "의 카드를 만듭니다.");
        this.owner = owner;
    }

    public void use() {
        System.out.println(owner + "의 카드를 사용합니다.");
    }
}
```

카드를 생성하는 Client
```java
public class Client {

    public void createAndUseCard() {
        // 팩토리를 통해서 IDCard를 생성한다.
        Factory factory = new IDCardFactory();
        Product card = factory.create("홍길동");

        card.use();
    }
}
```
