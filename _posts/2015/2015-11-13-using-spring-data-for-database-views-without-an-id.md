---
layout: post
title: "Using Spring Data for database views without an ID"
date: "2015-11-13"
categories: 
  - "coding"
  - "database"
  - "java"
  - "spring"
  - "sql"
---

A database view is a great way to prepare complicated data structures and present it in a more convenient way. 
We can use database views to map our technical driven database structure (with a lot of tables and foreign keys) to a more business driven structure. 
This means that we might join tables, concatenate values or just rename the columns, because a particular name suits better to the current business context.

```sql
CREATE OR REPLACE VIEW COMPANY_TO_PERSON AS 
SELECT
  COMPANY.NAME AS COMPANY_NAME,
  PERSON.NAME AS PERSON_NAME
FROM
  COMPANY
LEFT JOIN PERSON ON
  COMPANY.COMPANY_ID = PERSON.COMPANY_ID;
```

To map those views to Java entities, we can use [Spring Data](https://spring.io/projects/spring-data):

```java
@Entity
@Table(name = "COMPANY_TO_PERSON")  // A view!
public class Company2Person {

    // This won't work as the entity has no @Id!

    @Column(name = "COMPANY_NAME")
    private String companyName;

    @Column(name = "PERSON_NAME")
    private String personName;

    // getter and setter...
}
```

However, the example above wouldn't work. 
Spring will complain that the `@Id` annotation is missing and would throw a `org.hibernate.AnnotationException: No identifier specified for entity` exception.

From a business perspective, this is bad. 
The id of a _company_ is its _company id_, the id of a _person_ is its _person id_. 
But both are missing in the view, which has no unique identifier.

We could solve this problem by a simple trick: 
we invent a unique identifier - our current row number!

```sql
CREATE OR REPLACE VIEW COMPANY_TO_PERSON AS 
SELECT
  rownum AS ID,
  COMPANY.NAME AS COMPANY_NAME,
  PERSON.NAME AS PERSON_NAME
FROM
  COMPANY
LEFT JOIN PERSON ON
  COMPANY.COMPANY_ID = PERSON.COMPANY_ID;
```

We can now use the row number in our Spring Data entity:

```java
@Entity
@Table(name = "COMPANY_TO_PERSON")  // A view!
public class Company2Person {

    @Id
    @Column(name = "ID")
    private Long id; // The row number!

    @Column(name = "COMPANY_NAME")
    private String companyName;

    @Column(name = "PERSON_NAME")
    private String personName;

    // getter and setter...
}
```

**Best regards,** Thomas.
