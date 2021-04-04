---
title: 'AWS CloudWatch logging with Spring Boot'
layout: post
categories: springboot
---

## AWS 권한 추가
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

## dependency 추가
```kotlin
implementation("org.slf4j:slf4j-api:1.7.30")
implementation("ca.pjer:logback-awslogs-appender:1.4.0")
```

## logback-spring.xml에 설정 추가
```xml
<!-- to allow the queue to flush on exit -->
<shutdownHook class="ch.qos.logback.core.hook.DelayingShutdownHook"/>
<!-- Timestamp used into the Log Stream Name -->
<timestamp key="timestamp" datePattern="yyyyMMddHHmmssSSS"/>
<appender name="ASYNC_AWS_LOGS" class="ca.pjer.logback.AwsLogsAppender">
    <!-- Send only WARN and above -->
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
        <springProfile name="local">
            <level>DEBUG</level>
        </springProfile>
        <springProfile name="prod">
            <level>INFO</level>
        </springProfile>
    </filter>
    <!-- Nice layout pattern -->
    <layout>
        <pattern>%d{yyyyMMdd'T'HHmmss} %thread %level %logger{15} %msg%n
        </pattern>
    </layout>
    <!-- Hardcoded Log Group Name -->
    <!-- Timestamped Log Stream Name -->
    <springProfile name="local">
        <logGroupName>/test-local-api</logGroupName>
    </springProfile>
    <springProfile name="prod">
        <logGroupName>/test-op-api</logGroupName>
    </springProfile>
    <logStreamName>test-api-${timestamp}</logStreamName>

    <!-- Hardcoded AWS region -->
    <!-- So even when running inside an AWS instance in us-west-1, logs will go to us-west-2 -->
    <logRegion>ap-northeast-2</logRegion>
    <!-- Maximum number of events in each batch (50 is the default) -->
    <!-- will flush when the event queue has 50 elements, even if still in quiet time (see maxFlushTimeMillis) -->
    <maxBatchLogEvents>50</maxBatchLogEvents>
    <!-- Maximum quiet time in millisecond (0 is the default) -->
    <!-- will flush when met, even if the batch size is not met (see maxBatchLogEvents) -->
    <maxFlushTimeMillis>30000</maxFlushTimeMillis>
    <!-- Maximum block time in millisecond (5000 is the default) -->
    <!-- when > 0: this is the maximum time the logging thread will wait for the logger, -->
    <!-- when == 0: the logging thread will never wait for the logger, discarding events while the queue is full -->
    <maxBlockTimeMillis>5000</maxBlockTimeMillis>
</appender>

<!--    <root level="INFO">-->
<!--        <appender-ref ref="ASYNC_AWS_LOGS"/>-->
<!--    </root>-->

<logger name="AWS_LOGGER" level="INFO">
    <appender-ref ref="ASYNC_AWS_LOGS"/>
</logger>
```

## Reference
- <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html#running-ec2-step-1>
- <https://medium.com/@sungmok.sohn/aws-ec2%EC%9D%98-application-log%EB%A5%BC-s3%EB%A1%9C-%EB%B0%B1%EC%97%85%ED%95%98%EA%B8%B0-67ff02be5e2c>
