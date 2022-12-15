---
layout: post
title: "Testing @ConfigurationProperties in Spring Boot 2.5.X"
date: "2021-07-06"
categories:
  - "java"
  - "spring"
published: true
---

Three years ago, I wrote an article on how to test Spring's `@ConfigurationProperties` classes. Since three years are a long time, I just updated the project on GitHub. **It works with Spring 2.5.X now!**

You can find the project right here: 
- [https://github.com/tuhrig/Testing\_Configuration\_Properties](https://github.com/tuhrig/Testing_Configuration_Properties)

And here's the old post from 2018: 
- [https://tuhrig.de/testing-configurationproperties-in-spring-boot](https://tuhrig.de/testing-configurationproperties-in-spring-boot)

## What's it about?

The basic idea is that you have a configuration class which looks like this:

```java
@Data
@ConfigurationProperties(prefix = "my.properties")
public class MyConfigurationProperties {
    @NotNull
    @Length(min = 4)
    private String someMandatoryProperty;
    private String someOptionalProperty;
    private String someDefaultProperty = "default value";
    // many more...
}
```

This class binds to a YAML-configuration file which might look like this:

```yaml
my:
  properties:
    some_mandatory_property: "123456"
    some_optional_property: "abcdef"
    some_default_property: "overwritten"
```

Eventhough this is a very simple example, how would you test the validation of your configuration class? 
How would you test that the first property cannot be `null` and must have atleast four characters?

Usually, you would write a test like this:

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = { MyConfigurationPropertiesTest_1.TestConfiguration.class })
public class MyConfigurationPropertiesTest {

    @Autowired
    private MyConfigurationProperties properties;
 
    @Test
    public void should_Populate_MyConfigurationProperties() {
        assertThat(properties.getSomeMandatoryProperty()).isEqualTo("123456");
        ...
    }
 
    @EnableConfigurationProperties(MyConfigurationProperties.class)
    public static class TestConfiguration {
        // nothing
    }
}
```

So what's wrong with this? 
Well, you can only test one particular configuration with this. 
The whole test case loads a single YAML-file and let's you autowire a single `MyConfigurationProperties` object. 
This makes it very annoying to test different variations of a configuration file. 
You need a whole new test case for each little change. 
This is where my simple little helper comes into play.

## Using a builder for the properties

The [project on GitHub](https://github.com/tuhrig/Testing_Configuration_Properties) shows how to implement a little helper in order to build the configuration properties new in every single test. You can just load any properties file (e.g. `application.yml`) and manipulate its values to easily create different configurations for your test. This helps to reduce the amount of code you need a lot.

```java
MyConfigurationProperties properties = ConfigurationPropertiesBuilder
        .builder()
        .populate(MyConfigurationProperties.class)
        .fromFile("application.yml")
        .withPrefix("my.properties")
        .validateUsing(localValidatorFactoryBean)
        .withProperty("my.properties.some-optional-property", "abcdef")
        .withProperty("my.properties.some-default-property", "overwritten")
        .build();

assertThat(properties.getSomeMandatoryProperty()).isEqualTo("123456");
assertThat(properties.getSomeOptionalProperty()).isEqualTo("abcdef");
assertThat(properties.getSomeDefaultProperty()).isEqualTo("overwritten"); 
```

**Best regards,** Thomas.
