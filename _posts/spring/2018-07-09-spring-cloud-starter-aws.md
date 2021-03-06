---
title: '[Spring Cloud AWS] Spring Boot auto-configuration'
layout: post
categories: spring
---

```yaml
cloud:
    aws:
        credentials:
            accessKey: accessKey
            secretKey: secretKey
        region:
            static: ap-northeast-2
        stack:
            auto: false
```

## region
- cloud.aws.region.auto: EC2 meta data service로부터 region을 자동으로 찾을지 여부를 설정한다.
- cloud.aws.region.static: 애플리케이션의 static region을 설정한다.

## CloudFormation
- cloud.aws.stack.auto: CloudFormation의 stack 이름을 자동으로 찾을지 여부를 설정한다.  

## Reference
- <https://cloud.spring.io/spring-cloud-aws/2.1.x/multi/multi_spring-cloud-aws.html>