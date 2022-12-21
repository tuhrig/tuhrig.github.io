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

A database view is a great way to prepare complicated data structures right in the database and present it in a more convenient way. We usually use database views to map our technical driven database structure (with a lot of tables and foreign keys) to a more business driven structure. This means that we might join tables, concatenate values or just rename the columns, because a particular name suits better to the current business context.

CREATE OR REPLACE VIEW COMPANY\_TO\_PERSON AS 
SELECT
  COMPANY.NAME AS COMPANY\_NAME,
  PERSON.NAME AS PERSON\_NAME
FROM
  COMPANY
LEFT JOIN PERSON ON
  COMPANY.COMPANY\_ID = PERSON.COMPANY\_ID;

To map those views to Java entities, we simply use Spring data:

@Entity
@Table(name = "COMPANY\_TO\_PERSON")  // A view!
public class Company2Person {

    // This won't work as the entity has no @Id!

    @Column(name = "COMPANY\_NAME")
    private String companyName;

    @Column(name = "PERSON\_NAME")
    private String personName;

    // getter and setter...
}

However, the example above wouldn't work. Spring would complain that the `@Id` annotation is missing and would throw a `org.hibernate.AnnotationException: No identifier specified for entity` exception.

From a business perspective, this is bad. The id of a _company_ is its _company id_, the id of a _person_ is its _person id_. But both are missing in the view, which has not unique identifier.

We could solve this problem by a simple trick: we invent an unique identifier - our current row number!

CREATE OR REPLACE VIEW COMPANY\_TO\_PERSON AS 
SELECT
  rownum AS ID,
  COMPANY.NAME AS COMPANY\_NAME,
  PERSON.NAME AS PERSON\_NAME
FROM
  COMPANY
LEFT JOIN PERSON ON
  COMPANY.COMPANY\_ID = PERSON.COMPANY\_ID;

We can now use the row number in our Spring Data entity:

@Entity
@Table(name = "COMPANY\_TO\_PERSON")  // A view!
public class Company2Person {

    @Id
    @Column(name = "ID")
    private Long id; // The row number!

    @Column(name = "COMPANY\_NAME")
    private String companyName;

    @Column(name = "PERSON\_NAME")
    private String personName;

    // getter and setter...
}

**Best regards,** Thomas
