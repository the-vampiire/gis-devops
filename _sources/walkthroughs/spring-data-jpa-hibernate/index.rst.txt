:orphan:

.. _spring-data-jpa-hibernate-walkthrough:

====================================================
Walkthrough: Cars Demo - Spring Data, JPA, Hibernate
====================================================

In this walkthrough, we revisit the `Car Integration Tests <https://gitlab.com/LaunchCodeTraining/car-integration-test-starter>`_ that we worked on in yesterday's walkthrough :ref:`SIT-walkthrough`. We'll refactor the app to use Postgres and Hibernate.

Setup
-----

1. In the terminal go to project folder for **Car Integration Tests**
2. Checkout the branch that contains the solution for the Day 3 walkthrough. Example: ``$ git checkout master`` or ``$ git checkout solution``
3. Create a new branch for today's work: ``$ git checkout -b day4-add-hibernate``

Our Tasks
---------

Each section outlines one task or group of tasks we need to complete.

Set Up A Postgres DB
====================

1. From ``psql``, create a Postgres user: ``psql=# create user car_user with encrypted password 'catdogbluehouse';``
2. Create a database: ``psql=# create database car;``
3. Grant the user access to the database:``psql=# grant all privileges on database car to car_user``


Configure the App to use the Database
=====================================

To configure our Spring application to run with Postgres we will need to:
    - Update our gradle dependencies
    - Configure our database configurations in application.properties
    - Mark our models with the ``@Entity`` annotation
    - Create new interfaces that extend JpaRepository

Check out the :ref:`spring-postgres` to learn how to configure our Spring app to work with Postgres.

We also will be working with sensitive information, and it would be a good idea to read :ref:`environment-variables-intellij`.

In that article we:
    - Created an application.properties file
    - Added configurations about our database to the file
    - Used environment variable tokens to keep our database information secure
    - Added environment variables to our runtime configuration

.. note::

   Committing passwords to source control is a BAD idea. We are using a spring data feature that searches for and uses enviroment variables. `Read more about Spring Boot/Data configuration. <https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html#boot-features-external-config>`_
   
Add Annotations to Car Model
============================

.. code-block:: java

    package org.launchcode.training.models;

    import javax.persistence.Entity;
    import javax.persistence.GeneratedValue;
    import javax.persistence.GenerationType;
    import javax.persistence.Id;

    @Entity
    public class Car {

        @Id
        @GeneratedValue(strategy= GenerationType.IDENTITY)
        private int id;
        //rest of class is not shown...


Add CarRepository
=================

.. code-block:: java

    package org.launchcode.training.data;

    import org.launchcode.training.models.Car;
    import org.springframework.data.jpa.repository.JpaRepository;
    import org.springframework.stereotype.Repository;

    @Repository
    public interface CarRepository extends JpaRepository<Car, Integer> {
    }

Autowire Repositories into Controllers
======================================

.. code-block:: java

    @Controller
    @RequestMapping("car")
    public class CarController {

        @Autowired
        private org.launchcode.training.data.CarRepository carRepository;


Autowire Repositories in Tests
==============================

.. code-block:: java

    @RunWith(SpringRunner.class)
    @IntegrationTestConfig
    public class CarControllerTests {

        @Autowired
        private MockMvc mockMvc;

        @Autowired
        private CarRepository carRepository;

Configure Test DB
=================

Add a ``/car-integration-tests/src/test/resources/application-test.properties`` file with below contents.

.. code-block:: java

    spring.datasource.driver-class-name=org.h2.Driver
    spring.datasource.url=jdbc:h2:mem:test
    spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.H2Dialect
    spring.jpa.hibernate.ddl-auto=update

We need to make sure our test properaties are used when running tests. Add the below code to ``/car-integration-tests/src/test/java/org/launchcode/training/IntegrationTestConfig.java``.
The ``@Transactional`` annotation insures that any sql executed during a test will only exist for that single test and won't pollute another test.

.. code-block:: java

    @TestPropertySource(locations = "classpath:application-test.properties")
    @Transactional

Do the Tests Pass?
==================
If not, fix them ;p
