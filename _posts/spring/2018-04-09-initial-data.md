---
title: 'spring boot 데이터 초기화'
layout: post
categories: spring
---

## resources 폴더 아래에 data.sql 파일을 추가한다.
data.sql
```sql
INSERT INTO myshop_member
(member_id, name, password)
VALUES('a0984e75-2bfc-449f-b0f6-9f1d3081d4c2', 'tester', '1f62a744-da77-498c-9ec7-3f5e3aecefc7');

INSERT INTO product
(name, price)
VALUES('노트북', '1000000');
```

## application.yml 파일에 설정을 추가한다.
```yaml
spring:
    datasource:
        initialization-mode: embedded
```
상황에 맞게 embedded나 always를 추가한다.


## Reference
- <https://medium.com/the-full-stack-developer/how-to-set-up-initial-data-while-launching-a-spring-boot-application-c3e4c0a56b42>