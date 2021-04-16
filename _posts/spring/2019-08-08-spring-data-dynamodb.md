---
title: 'Spring Data DynamoDB'
layout: post
categories: spring
---

## Source
- <https://github.com/kjgleh/spring-data-dynamodb>

## Dependencies
```kotlin
// kotlinFixture
testImplementation("com.appmattus.fixture:fixture:1.1.0")

// spring data dynamodb
implementation("io.github.boostchicken:spring-data-dynamodb:5.2.5")

// aws
implementation("org.springframework.cloud:spring-cloud-starter-aws:2.2.6.RELEASE")

// localstack
implementation("org.testcontainers:localstack:1.15.2")
testImplementation("org.testcontainers:junit-jupiter:1.15.2")
```

## application.yml
```yaml
spring:
  profiles:
      active: local

cloud:
    aws:
        region:
            static: ap-northeast-2
        stack:
            auto: false
```

## application-local.yml
```yaml
spring:
    data:
        dynamodb:
            entity2ddl:
                auto: create
                gsiProjectionType: ALL
                readCapacity: 10
                writeCapacity: 1

```

## application-prod.yml
```yaml
cloud:
    aws:
        credentials:
            accessKey: accessKey
            secretKey: secretKey
```

## local configuration
```kotlin
@Configuration
@Profile("local")
@EnableDynamoDBRepositories(basePackages = ["me.kjgleh.springdatadynamodb.order"])
class DynamoDBLocalConfig {

    private val localstackImage =
        DockerImageName.parse("localstack/localstack:latest")

    @Bean
    @Primary
    fun dynamoDBMapper(amazonDynamoDB: AmazonDynamoDB): DynamoDBMapper {
        return DynamoDBMapper(amazonDynamoDB, DynamoDBMapperConfig.DEFAULT)
    }

    @Bean(initMethod = "start", destroyMethod = "stop")
    fun localStackContainer(): LocalStackContainer {
        return LocalStackContainer(localstackImage).withServices(
            LocalStackContainer.Service.DYNAMODB
        )
    }

    @Bean
    fun amazonDynamoDB(localStackContainer: LocalStackContainer): AmazonDynamoDB {
        return AmazonDynamoDBClientBuilder.standard()
            .withEndpointConfiguration(
                localStackContainer.getEndpointConfiguration(
                    LocalStackContainer.Service.DYNAMODB
                )
            )
            .withCredentials(localStackContainer.defaultCredentialsProvider)
            .build()
    }
}
```

## prod configuration
```kotlin
@Configuration
@Profile("prod")
@EnableDynamoDBRepositories(basePackages = ["me.kjgleh.springdatadynamodb.order"])
class DynamoDBConfig(
    @Value("\${amazon.aws.accessKey}")
    private var amazonAwsAccessKey: String,
    @Value("\${amazon.aws.secretKey}")
    private var amazonAwsSecretKey: String
) {

    @Bean
    @Primary
    fun dynamoDBMapper(amazonDynamoDB: AmazonDynamoDB): DynamoDBMapper {
        return DynamoDBMapper(amazonDynamoDB, DynamoDBMapperConfig.DEFAULT)
    }

    @Bean
    fun amazonDynamoDB(): AmazonDynamoDB {
        val credentialsProvider = AWSStaticCredentialsProvider(
            BasicAWSCredentials(amazonAwsAccessKey, amazonAwsSecretKey)
        )

        return AmazonDynamoDBClientBuilder.standard()
            .withCredentials(credentialsProvider)
            .withRegion(Regions.AP_NORTHEAST_2)
            .build()
    }
}
```

## Entity
```kotlin
@DynamoDBTable(tableName = "order")
class Order(
    @Id
    @DynamoDBHashKey
    var id: String = UUID.randomUUID().toString(),

    @DynamoDBAttribute
    var userId: String = "",

    @DynamoDBAttribute
    var orderLines: List<OrderLine> = listOf(),

    @DynamoDBAttribute
    @DynamoDBTypeConverted(converter = LocalDateTimeConverter::class)
    var createdAt: LocalDateTime = LocalDateTime.now()
)
```

```kotlin
@DynamoDBDocument
class OrderLine(
    @DynamoDBAttribute
    var quantity: Int = 0
)
```

## LocalDateTimeConverter
```kotlin
class LocalDateTimeConverter : DynamoDBTypeConverter<Date, LocalDateTime> {

    override fun convert(source: LocalDateTime): Date {
        return Date.from(source.atZone(ZoneId.systemDefault()).toInstant())
    }

    override fun unconvert(source: Date): LocalDateTime {
        return source.toInstant().atZone(TimeZone.getDefault().toZoneId())
            .toLocalDateTime()
    }
}
```

## Repository
```kotlin
@EnableScan
interface OrderRepository : CrudRepository<Order, String>
```

## Test
```kotlin
@SpringBootTest(classes = [DynamoDBLocalConfig::class])
class OrderRepositoryTest constructor(
    @Autowired
    private var orderRepository: OrderRepository
) {

    companion object {
        private val fixture = kotlinFixture {
            recursionStrategy(NullRecursionStrategy)
        }
    }

    @Test
    fun `find order correctly`() {
        // Arrange
        val orderLine = fixture<OrderLine>()
        val order = fixture<Order> {
            property(Order::orderLines) { listOf(orderLine) }
        }
        val id = orderRepository.save(order).id

        // Act
        val sut = orderRepository
        val orderSaved = sut.findById(id).orElseThrow { Exception() }

        // Assert
        assertThat(orderSaved).usingRecursiveComparison()
            .ignoringFields("createdAt").isEqualTo(order)
    }
}
```

## 파라미터가 없는 생성자가 없는 경우 에러가 발생한다.
```text
NoSuchMethodException .<init>()
```

## 프로퍼티가 val인 경우 에러가 발생한다.
```text
java.lang.NullPointerException
at com.amazonaws.services.dynamodbv2.datamodeling.StandardBeanProperties$MethodReflect.set(StandardBeanProperties.java:133)
```

## Date 타입만 지원하기 때문에 LocalDateTime 사용시 에러가 발생한다.
```text
not supported; requires @DynamoDBTyped or @DynamoDBTypeConverted
```

## EC2 metadata resolution related exception thrown when running application locally
- <https://github.com/spring-cloud/spring-cloud-aws/issues/556>
```kotlin
@Configuration
@EnableAutoConfiguration(exclude = [ContextInstanceDataAutoConfiguration::class])
class AutoConfigCustom
```

