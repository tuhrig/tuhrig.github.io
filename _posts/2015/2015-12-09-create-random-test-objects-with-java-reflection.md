---
layout: post
title: "Create random test objects with Java reflection"
date: "2015-12-09"
categories: 
  - "coding"
  - "java"
---

Although Java is an object-oriented language, you will often separate our data and your actual business logic. 
You will write POJOs, entities, domain models and DTOs which you will pass to services, repositories and controllers. 
If you do so, you will properly need test data for those objects. 
Often, the data you pass will be very important as it represents the state of your application and business process. 
You must design it carefully to get the test results you are looking for. 
However, there are also some cases where the data isn't so important - it must just be there! 
You need an object, filled with random data. 
That's it.

Java's reflection mechanism offers an easy method to create POJOs filled with random data. 
Here is a basic code snippet which will take a class, instantiate it and fills its fields with random data. 
Maybe it makes your test setup easier.

````java
import java.lang.reflect.Field;
import java.math.BigInteger;
import java.util.Random;
import java.util.UUID;

public class RandomObjectFiller {

    private Random random = new Random();

    public T createAndFill(Class clazz) throws Exception {
        T instance = clazz.newInstance();
        for(Field field: clazz.getDeclaredFields()) {
            field.setAccessible(true);
            Object value = getRandomValueForField(field);
            field.set(instance, value);
        }
        return instance;
    }

    private Object getRandomValueForField(Field field) throws Exception {
        Class type = field.getType();

        // Note that we must handle the different types here! This is just an 
        // example, so this list is not complete! Adapt this to your needs!
        if(type.isEnum()) {
            Object\[\] enumValues = type.getEnumConstants();
            return enumValues\[random.nextInt(enumValues.length)\];
        } else if(type.equals(Integer.TYPE) || type.equals(Integer.class)) {
            return random.nextInt();
        } else if(type.equals(Long.TYPE) || type.equals(Long.class)) {
            return random.nextLong();
        } else if(type.equals(Double.TYPE) || type.equals(Double.class)) {
            return random.nextDouble();
        } else if(type.equals(Float.TYPE) || type.equals(Float.class)) {
            return random.nextFloat();
        } else if(type.equals(String.class)) {
            return UUID.randomUUID().toString();
        } else if(type.equals(BigInteger.class)){
            return BigInteger.valueOf(random.nextInt());
        }
        return createAndFill(type);
    }
} 
````

**Best regards,** Thomas.
