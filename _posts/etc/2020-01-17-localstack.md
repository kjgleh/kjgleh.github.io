---
title: '로컬에서 LocalStack 구동하기'
layout: post
categories: etc
---

## LocalStack이란
로컬에서 테스트할 수 있는 AWS 클라우드 환경을 제공한다.

## LocalStack 실행
원하는 경로에(~/docker) docker-compose.yml 파일을 생성한다.
```yaml
version: '2.1'

services:
    localstack:
        container_name: "${LOCALSTACK_DOCKER_NAME-localstack_main}"
        image: localstack/localstack
        network_mode: bridge
        ports:
            - "4567-4597:4567-4597"
            - "${PORT_WEB_UI-8080}:${PORT_WEB_UI-8080}"
        environment:
            - SERVICES=${SERVICES- }
            - DEBUG=${DEBUG- }
            - DATA_DIR=${DATA_DIR- }
            - PORT_WEB_UI=${PORT_WEB_UI- }
            - LAMBDA_EXECUTOR=${LAMBDA_EXECUTOR- }
            - KINESIS_ERROR_PROBABILITY=${KINESIS_ERROR_PROBABILITY- }
            - DOCKER_HOST=unix:///var/run/docker.sock
            - HOST_TMP_FOLDER=${TMPDIR}
        volumes:
            - "${TMPDIR:-/tmp/localstack}:/tmp/localstack"
            - "/var/run/docker.sock:/var/run/docker.sock"
```
docker-compose.yml 파일의 경로에서 docker-compose를 사용하여 환경을 구성한다.
```
export SERVICES=s3,sns,sqs,dynamodb //구성하려는 서비스 추가
export PORT_WEB_UI=8080 //포트를 변경할 수 있다.
export DEBUG=0 
docker-compose up -d
```

참고)AWS 서비스별 포트
4567:4567   # apigateway
4568:4568   # kinesis
4569:4569   # dynamodb
4570:4570   # dynamodbstreams
4571:4571   # elasticache
4572:4572   # s3
4573:4573   # firehose
4574:4574   # lambda
4576:4576   # sqs
4597:4597   # ec2

아래 에러가 발생하면 도커 설정을 변경해 준다.
```
ERROR: for localstack  Cannot create container for service localstack: C: drive is not shared. Please share it in Docker for Windows Settings
Encountered errors while bringing up the project.
```

명령창에 아래 명령을 실행한다
```text
set COMPOSE_CONVERT_WINDOWS_PATHS=1
```
도커를 재시작한 도커 설정을 변경한다.
Go to Docker for Windows settings > Shared Drives > Reset credentials > select drive > Apply

도커의 localstack 컨테이너를 삭제하고 재시작한다.

## Reference
- <https://github.com/localstack/localstack>
- <https://reflectoring.io/aws-localstack/>
- <https://www.44bits.io/ko/post/almost-perfect-development-environment-with-docker-and-docker-compose#%EB%8F%84%EC%BB%A4-%EC%BB%B4%ED%8F%AC%EC%A6%88%EB%A1%9C-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%84%B1%ED%95%98%EA%B8%B0>
- <https://woowabros.github.io/tools/2019/07/18/localstack-integration.html>
- <https://github.com/Cyb3rWard0g/HELK/issues/79>


