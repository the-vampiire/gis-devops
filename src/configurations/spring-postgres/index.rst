:orphan:

.. _spring-postgres:

================================
Configuration: Spring & Postgres
================================

One of the many benefits of Spring is that it contains a vast amount of tools we can leverage while building our web applicatoins. One of these tools is Spring Data JPA. Spring Data JPA has some code that will connect to our database, and perform basic operations by using methods attached to the JPA Repository.

JPA Repositories make it very easy for us to work with various data stores including SQL data stores like Postgres, and NoSQL data stores like ElasticSearch.

To get our web applicatoin to work with JPA Repository we need to do a couple of things.

1. Include Spring Data in our dependencies (this class uses the build.gradle file to manage our dependencies)
2. Configure our database connection information in our application.properties file
3. Map our classes to our tables using Hibernate
4. Extend the JPA Repository interface