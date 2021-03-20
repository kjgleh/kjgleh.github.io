---
title: 'AWS Secret Manager로 property 관리'
layout: post
categories: spring
---

## AWS console에서 Secret 생성
![](/asset/images/spring/aws-secret-manager_01.png)
![](/asset/images/spring/aws-secret-manager_02.png)
Secret name은 /secret로 시작되어야 한다.
[Spring Cloud Document](https://cloud.spring.io/spring-cloud-static/spring-cloud-aws/2.1.0.RELEASE/single/spring-cloud-aws.html#_integrating_your_spring_cloud_application_with_the_aws_secrets_manager)
에서 확인해보면 aws.secretsmanager.prefix의 default값이 /secret이다.

## dependency를 추가한다.
```kotlin
implementation("org.springframework.cloud:spring-cloud-context:2.2.6.RELEASE")
implementation("org.springframework.cloud:spring-cloud-starter-aws-secrets-manager-config:2.2.6.RELEASE")
```

## Resources 폴더에 bootstrap.yml을 추가한다.
```yaml
aws:
    secretsmanager:
        name: backend
cloud:
    aws:
        region:
            static: ap-northeast-2
```
aws.secretsmanager.name은 AWS console에서 생성한 Secret name에서 /secret/을 제외한 값이다.

## reference
- <https://cloud.spring.io/spring-cloud-static/spring-cloud-aws/2.1.0.RELEASE/single/spring-cloud-aws.html#_integrating_your_spring_cloud_application_with_the_aws_secrets_manager>
- <https://blog.usejournal.com/aws-secret-manager-service-as-application-properties-with-spring-boot-f46fe6bd44f7>



