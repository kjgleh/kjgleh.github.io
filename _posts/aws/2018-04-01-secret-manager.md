---
title: 'AWS Secret Manager로 RDS 계정 및 비밀번호 관리'
layout: post
categories: aws
---

## AWS console에서 Secret 생성
- type: Credentials for RDS database

## dependency 추가
```kotlin
implementation("com.amazonaws.secretsmanager:aws-secretsmanager-jdbc:1.0.6")
```

## application.yml에 설정 추가
```yaml
spring:
    datasource:
        url: jdbc-secretsmanager:mysql://host:port/dbName
        username: secretName
        driver-class-name: com.amazonaws.secretsmanager.sql.AWSSecretsManagerMySQLDriver
```

## 에러
```text
com.amazonaws.services.secretsmanager.model.AWSSecretsManagerException: User: arn:aws:iam::xxx is not authorized to perform: secretsmanager:DescribeSecret on resource: secret/name
```
Access key 소유 계정에 SecretsManagerReadWrite 권한을 준다.

## reference
- <https://dzone.com/articles/spring-boot-handle-aws-rds-password-change-or-rota>



