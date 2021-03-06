---
title: '[Spring Cloud AWS] Unable to retrieve the requested metadata'
layout: post
categories: spring
---

Spring Cloud AWS를 스프링 부트에서 실행하면 아래 경고가 발생한다.
```text
com.amazonaws.util.EC2MetadataUtils      : Unable to retrieve the requested metadata (/latest/meta-data/instance-id).
```  

Amazon SDK에서 발생하는 예외로 임시로 아래 두가지 방법으로 해결
- com.amazonaws의 로그 레벨을 ERROR 레벨로 변경한다.
- 실행중인 ec2 인스턴스의 인스턴스 데이터가 필요하지 않다면 아래와 같이 설정을 추가한다.

```kotlin
@Configuration
@EnableAutoConfiguration(exclude = [ContextInstanceDataAutoConfiguration::class])
class AutoConfigCustom
```

## Reference
- <https://stackoverflow.com/questions/56987939/unable-to-retrieve-the-requested-metadata-latest-meta-data-public-hostname>