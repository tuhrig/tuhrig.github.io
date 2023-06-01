---
layout: post
title: "Testing @ConfigurationProperties in Spring Boot"
date: "2018-05-30"
categories: 
  - "coding"
  - "spring"
---

> **Update**: I updated the [example on GitHub](https://github.com/tuhrig/Testing_Configuration_Properties) to Spring 2.5.X! 
> The latest post on this topic is [here](/testing-configurationproperties-in-spring-boot-2-5-x).

I recently worked on [a library](https://github.com/tuhrig/aws-kinesis-spring-boot-starter) for using [AWS Kinesis](https://aws.amazon.com/kinesis) in [Spring Boot](https://projects.spring.io/spring-boot/). 
As many other libraries, this particular one provided a powerful configuration. 
To implement the configuration, we used Spring Boot's [@ConfigurationProperties](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/context/properties/ConfigurationProperties.html) (as described [here](/using-configurationproperties-to-separate-service-and-configuration/)). 
This article gives some insights on how we did our testing.

You can find the source code on GitHub:

> [https://github.com/tuhrig/Testing_Configuration_Properties](https://github.com/tuhrig/Testing_Configuration_Properties)

## The configuration

Our configuration wasn't very complicated - it was just large. 
Below you can see a simplified example. 
Some properties are mandatory while others are optional. 
Additionally, some properties have default values which can be overwritten.

```java
@Data
@ConfigurationProperties(prefix = "my.properties")
public class MyConfigurationProperties {

    @NotNull
    private String someMandatoryProperty;

    private String someOptionalProperty;

    private String someDefaultProperty = "default value";

    // many more...
}
```

(View on [GitHub](https://github.com/tuhrig/Testing_Configuration_Properties/blob/master/src/test/java/MyConfigurationProperties.java))

## The happy path

We started by testing the happy path. 
The test was pretty simple and worked well. 
You can see it below.

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = { MyConfigurationPropertiesTest_1.TestConfiguration.class })
@ActiveProfiles("happy-path")
public class MyConfigurationPropertiesTest_1 {

    @Autowired
    private MyConfigurationProperties properties;

    @Test
    public void should_Populate_MyConfigurationProperties() {
        assertThat(properties.getSomeMandatoryProperty()).isEqualTo("123456");
        assertThat(properties.getSomeOptionalProperty()).isEqualTo("abcdef");
        assertThat(properties.getSomeDefaultProperty()).isEqualTo("overwritten");
    }

    @EnableConfigurationProperties(MyConfigurationProperties.class)
    public static class TestConfiguration {
        // nothing
    }
}
```

With `application-happy-path.yml`:

```yaml
my:
  properties:
    some_mandatory_property: "123456"
    some_optional_property: "abcdef"
    some_default_property: "overwritten"
```

(View on [GitHub](https://github.com/tuhrig/Testing_Configuration_Properties/blob/master/src/test/java/MyConfigurationPropertiesTest_1.java))

## Different variations

Our next step was to test different variations of the configuration. 
Since some properties are optional, we wanted to test what will happen if they are missing. 
So we wrote another test:

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = { MyConfigurationPropertiesTest_2.TestConfiguration.class })
@ActiveProfiles("minimal")
public class MyConfigurationPropertiesTest_2 {

    @Autowired
    private MyConfigurationProperties properties;

    @Test
    public void should_Populate_MyConfigurationProperties() {
        assertThat(properties.getSomeMandatoryProperty()).isEqualTo("123456");
        assertThat(properties.getSomeOptionalProperty()).isEqualTo(null); /* null! */
        assertThat(properties.getSomeDefaultProperty()).isEqualTo("default value");
    }

    @EnableConfigurationProperties(MyConfigurationProperties.class)
    public static class TestConfiguration {
        // nothing
    }
}
```

With `application-minimal.yml`:

```yaml
    my:
      properties:
        some_mandatory_property: "123456"
```

(View on [GitHub](https://github.com/tuhrig/Testing_Configuration_Properties/blob/master/src/test/java/MyConfigurationPropertiesTest_2.java))

## The problem

However, there was a problem with this approach: 
we needed to write a complete test class for each scenario:

- `MyConfigurationPropertiesTest_1.java`
- `MyConfigurationPropertiesTest_2.java` 
- ...and so on... 

Each variation would require us to create a new class _and_ a new `application-xxx.yml` file. 
That wasn't to end well.

## Builder for @ConfigurationProperties

Our solution was to load the `@ConfigurationProperties` programmatically instead of just wiring them with Spring. 
This gave us the possibility to modify them in each test. 
To make life even easier we wrote a `ConfigurationPropertiesBuilder` for our test cases. 
You can see the result below. 
We accomplished to test a lot of cases in a single test:

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = { ValidationAutoConfiguration.class })
public class MyConfigurationPropertiesTest {
 
    @Autowired
    private LocalValidatorFactoryBean localValidatorFactoryBean;
 
    @Test
    public void should_Populate_MyConfigurationProperties() throws Exception {
 
        MyConfigurationProperties properties = ConfigurationPropertiesBuilder
                 .builder()
                 .populate(new MyConfigurationProperties())
                 .fromFile("application.yml")
                 .withPrefix("my.properties")
                 .validateUsing(localValidatorFactoryBean)
                 .withProperty("my.properties.some-optional-property", "abcdef")
                 .withProperty("my.properties.some-default-property", "overwritten")
                 .build();
 
        assertThat(properties.getSomeMandatoryProperty()).isEqualTo("123456");
        assertThat(properties.getSomeOptionalProperty()).isEqualTo("abcdef");
        assertThat(properties.getSomeDefaultProperty()).isEqualTo("overwritten");
    }
 
    @Test
    public void should_Populate_MyConfigurationProperties\_WithMandatoryPropertiesOnly() throws Exception {
 
        MyConfigurationProperties properties = ConfigurationPropertiesBuilder
                 .builder()
                 .populate(new MyConfigurationProperties())
                 .fromFile("application.yml")
                 .withPrefix("my.properties")
                 .validateUsing(localValidatorFactoryBean)
                 .build();
 
        assertThat(properties.getSomeMandatoryProperty()).isEqualTo("123456");
        assertThat(properties.getSomeOptionalProperty()).isEqualTo(null);
        assertThat(properties.getSomeDefaultProperty()).isEqualTo("default value");
    }
 
    @Test(expected = BindException.class)
    public void should_ThrowException_IfMandatoryPropertyIsMissing() throws Exception {
        ConfigurationPropertiesBuilder
                .builder()
                .populate(new MyConfigurationProperties())
                .fromFile("application.yml")
                .withPrefix("my.properties")
                .validateUsing(localValidatorFactoryBean)
                .withoutProperty("my.properties.some_mandatory_property")
                .build();
    }
}
```

With `application.yml`:

```yaml
    my:
      properties:
        some_mandatory_property: "123456" 
```

(View on [GitHub](https://github.com/tuhrig/Testing_Configuration_Properties/blob/master/src/test/java/MyConfigurationPropertiesTest.java))

The builder we used to set up our properties looks like below. 
It provides different options in order to build the properties. 
For example, you can set properties from a `YAML` file or programmatically.

```java
public class ConfigurationPropertiesBuilder {

    private T object;
    private String fileName;
    private String prefix;
    private Validator validator;
    private Properties properties = new Properties();
    private List propertiesToRemove = new ArrayList<>();

    public static ConfigurationPropertiesBuilder builder() {
        return new ConfigurationPropertiesBuilder();
    }

    public ConfigurationPropertiesBuilder populate(T object) {
        this.object = object;
        return this;
    }

    public ConfigurationPropertiesBuilder fromFile(String fileName) {
        this.fileName = fileName;
        return this;
    }

    public ConfigurationPropertiesBuilder withPrefix(String prefix) {
        this.prefix = prefix;
        return this;
    }

    public ConfigurationPropertiesBuilder validateUsing(Validator validator) {
        this.validator = validator;
        return this;
    }

    public ConfigurationPropertiesBuilder withProperty(String key, String value) {
        properties.setProperty(key, value);
        return this;
    }

    public ConfigurationPropertiesBuilder withoutProperty(String key) {
        propertiesToRemove.add(key);
        return this;
    }

    public T build() throws BindException {

        Properties propertiesFromFile = loadYamlProperties(fileName);
        propertiesToRemove.forEach(properties::remove);
        propertiesToRemove.forEach(propertiesFromFile::remove);

        MutablePropertySources propertySources = new MutablePropertySources();
        propertySources.addLast(new PropertiesPropertySource("properties", properties));
        propertySources.addLast(new PropertiesPropertySource("propertiesFromFile", propertiesFromFile));

        PropertiesConfigurationFactory configurationFactory = new PropertiesConfigurationFactory<>(object);
        configurationFactory.setPropertySources(propertySources);
        configurationFactory.setTargetName(prefix);
        configurationFactory.setValidator(validator);
        configurationFactory.bindPropertiesToTarget();

        return object;
    }

    private Properties loadYamlProperties(String fileName) {
        Resource resource = new ClassPathResource(fileName);
        YamlPropertiesFactoryBean factoryBean = new YamlPropertiesFactoryBean();
        factoryBean.setResources(resource);
        return factoryBean.getObject();
    }
} 
```

(View on [GitHub](https://github.com/tuhrig/Testing_Configuration_Properties/blob/master/src/test/java/ConfigurationPropertiesBuilder.java))

## Notes

In order to test the validation, we needed to add some dependencies to our project. 
Depending on your setup, this might be needed or not.

```groovy
    compile "javax.validation:validation-api:2.0.0.Final"

    testCompile "org.hibernate:hibernate-validator:6.0.8.Final"
    testCompile "org.glassfish:javax.el:3.0.0"
```

## Example on GitHub

I have published this example on GitHub:

[https://github.com/tuhrig/Testing_Configuration_Properties](https://github.com/tuhrig/Testing_Configuration_Properties)

**Best regards,** Thomas.
