---
title: '로컬에서 DynamoDB 사용하기'
layout: post
categories: aws
---

## 도커에 dynamodb 구동
```text
docker pull amazon/dynamodb-local

docker run -p 8000:8000 amazon/dynamodb-local
```

## AWS CLI 설정
```text
aws configure
```
아래 내용을 입력한다.
~/.AWS/credentails에 AWS Access Key ID 와 AWS Secret Access Key가 이미 설정되어 있는 경우 두 값은 입력하지 않아도 된다. 
```text
AWS Access Key ID [None]: ********************
AWS Secret Access Key [None]: ********************
Default region name [None]: local
Default output format [None]: json
```

## 테이블 확인
```text
$ aws dynamodb list-tables --endpoint-url http://localhost:8000
```
결과
```text
{
    "TableNames": []
}
```

## Reference
- <https://medium.com/@byeonggukgong/using-amazon-dynamodb-in-local-environment-feat-docker-fafbb420e161/>



