---
layout: post
title: "DynamoDB with Kotlin and Spring Boot (Part 1)"
date: "2018-10-15"
categories: 
  - "coding"
tags: 
  - "aws"
  - "database"
  - "dynamodb"
  - "kotlin"
  - "spring"
  - "spring-boot"
coverImage: "old-1075609_1280.jpg"
---

During the last week I implemented my first persistence layer with DynamoDB using Spring Boot and Kotlin. As I stumbled over a couple of obstacles, I decided to summarize what I did and how my final implementation looked like.

# Dependencies

The dependencies are pretty straight forward. I decided to use [Spring Data for DynamoDB](https://github.com/derjust/spring-data-dynamodb) and pulled in a single dependency to my `build.gradle`:

    com.github.derjust:spring-data-dynamodb:5.0.3

# Database configuration

Before you can use DynamoDB in Spring Boot you need to configure it - of course. I decided to provide two configurations, one for local development and one for production. For local development I'm running DynamoDB in Docker (using [richnorth/dynalite](https://hub.docker.com/r/richnorth/dynalite)). On production everything runs on AWS. You will find both configurations below.

I also decided to create the DynamoDB table on the fly as soon as the application starts. If the table already exists, DynamoDB will throw an error which catch and gracefully handle. This is the same approach the AWS SDK uses internally, for example in `com.amazonaws.services.kinesis.leases.impl.LeaseManager#createLeaseTableIfNotExists(...)`.

    @Configuration
    @EnableDynamoDBRepositories
    class DynamoDbConfiguration {
    
        private val log: Logger = LoggerFactory.getLogger(this.javaClass)
    
        @Profile("!dynamo-local")
        @Bean("amazonDynamoDB")
        fun dynamoDb(): AmazonDynamoDB {
            val client = AmazonDynamoDBClientBuilder.defaultClient()
            createTableForEntity(client, MyEntity::class)
            return client
        }
    
        @Profile("dynamo-local")
        @Bean("amazonDynamoDB")
        fun dynamoDbLocal(@Value("\\${aws.dynamodb.endpoint}") amazonDynamoDBEndpoint: String): AmazonDynamoDB {
            val client = AmazonDynamoDBClientBuilder
                .standard()
                .withCredentials(AWSStaticCredentialsProvider(BasicAWSCredentials("key", "secret")))
                .withEndpointConfiguration(AwsClientBuilder.EndpointConfiguration(amazonDynamoDBEndpoint, Regions.EU\_CENTRAL\_1.toString()))
                .build()
            createTableForEntity(client, MyEntity::class)
            return client
        }
    
        private fun createTableForEntity(amazonDynamoDB: AmazonDynamoDB, entity: KClass<\*>) {
    
            val tableRequest = DynamoDBMapper(amazonDynamoDB)
                .generateCreateTableRequest(entity.java)
                .withProvisionedThroughput(ProvisionedThroughput(1L, 1L))
    
            try {
                DynamoDB(amazonDynamoDB).createTable(tableRequest).waitForActive()
                log.info("Table created! \[entity={}\]", entity)
            } catch (e: ResourceInUseException) {
                log.info("Table already exists - skip creation! \[entity={}\]", entity)
            }
        }
    }

# Entity model

The entity model was the first tricky part. I wanted to use a [composite primary key](https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key) made up of a _hash key_ (aka **partition key**) and a _range key_ (aka **sort key**).

That's the point where two concepts seems to collide a little bit: Spring Data requires a property as an unique key annotated with `@Id` whereas DynamoDB requires getter and setter for both, hash key and range key.

There are two possible ways to solve this mismatch:

1. The first solution is to have an unique ID and additionally implement getter and setter for the hash key and range key. The getter and setter will "redirect" all access to the unique ID. You can see an example of that approach on the official Wiki of Spring Data DynamoDB: [https://github.com/derjust/spring-data-dynamodb/wiki/Use-Hash-Range-keys#domain-class](https://github.com/derjust/spring-data-dynamodb/wiki/Use-Hash-Range-keys#domain-class)
2. The second solution is the exact opposite. We implement two properties for hash key and range key and let our `@Id` "redirect" to those properties. Written in Kotlin this is far less code compared to the first solution.

    @DynamoDBTable(tableName = "MyEntity")
    data class MyEntity(
    
        @get:DynamoDBHashKey(attributeName = "group")
        var group: String? = null,
    
        @get:DynamoDBRangeKey(attributeName = "position")
        var position: String? = null,
    
        @DynamoDBAttribute(attributeName = "data")
        @AsJson
        var data: SomeObject? = null
    ) {
    
        @Id
        private var id: UniqueId? = null
            get() {
                return UniqueId(group, position)
            }
    }

    data class UniqueId(
        @DynamoDBHashKey
        var group: String? = null,
        @DynamoDBRangeKey
        var position: String? = null
    )

# Storing JSON

As my domain model was quite complex, I wanted to store parts of it as plain JSON. To do so, Amason's SDK provides a custom annotation called [`@DynamoDBTypeConvertedJson`](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/dynamodbv2/datamodeling/DynamoDBTypeConvertedJson.html). This annotation will take an object and takes care of the serialization and deserialization using [Jackson's `ObjectMapper`](https://www.baeldung.com/jackson-object-mapper-tutorial).

Unfortunately, the `ObjectMapper` cannot be injected. As an annotation cannot be a Spring-managed bean, the `ObjectMapper` is created with `new`. This is a problem if you want to (or need to) use a custom `ObjectMapper`.

To get rid of this problem, I created my own version of Amazon's `@DynamoDBTypeConvertedJson` and called it `AsJson`. It will get the `ObjectMapper` from outside - from a class called `ObjectMapperHolder`. This class is a small "hack" to get a static access to the `ObjectMapper` configured in the Spring Context.

    @DynamoDBTypeConverted(converter = Converter::class)
    @DynamoDBTyped(DynamoDBMapperFieldModel.DynamoDBAttributeType.S)
    @Retention(AnnotationRetention.RUNTIME)
    @Target(
        AnnotationTarget.CLASS,
        AnnotationTarget.FILE,
        AnnotationTarget.FIELD,
        AnnotationTarget.FUNCTION,
        AnnotationTarget.PROPERTY\_GETTER,
        AnnotationTarget.PROPERTY\_SETTER
    )
    annotation class AsJson(val targetType: KClass = Unit::class)
    
    class Converter(targetType: Class, annotation: AsJson) : DynamoDBTypeConverter<string, t=""> {
    
        private val targetType: KClass =
            if (annotation.targetType == Unit::class) targetType.kotlin else annotation.targetType as KClass
    
        override fun convert(obj: T): String {
            return ObjectMapperHolder.getObjectMapper().writeValueAsString(obj)
        }
    
        override fun unconvert(json: String): T {
            return ObjectMapperHolder.getObjectMapper().readValue(json, targetType.java)
        }
    }
    
    @Service
    class ObjectMapperHolder(private val objectMapper: ObjectMapper) {
    
        companion object {
            private var objectMapper: ObjectMapper? = null
            fun getObjectMapper(): ObjectMapper {
                return objectMapper
                    ?: throw NullPointerException("ObjectMapper not set - is the Spring context already up?")
            }
        }
    
        @PostConstruct
        fun useObjectMappper() {
            ObjectMapperHolder.objectMapper = objectMapper
        }
    }
</string,>

# More

- [https://github.com/derjust/spring-data-dynamodb](https://github.com/derjust/spring-data-dynamodb)
- [https://www.baeldung.com/spring-data-dynamodb](https://www.baeldung.com/spring-data-dynamodb)

**Best regards,** Thomas
