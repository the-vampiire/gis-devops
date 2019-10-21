:orphan:

.. _upgrading-spring-boot:

=========================================
Walkthrough: Upgrading to Spring Boot 2.x
=========================================

This walkthrough outlines the steps necessary to upgrade Spring Boot from a 1.x version to a 2.x release. The specific code referenced upgrades from 1.5.9 to 2.1.1, but the steps should work for other 1.x and 2.x versions as well.

We will upgrade the ``LaunchCodeTraining / launchcart`` app, but you should be able to follow these steps for other apps as well. You can view each of the specific changes made in this walkthrough in `this commit <https://gitlab.com/LaunchCodeTraining/launchcart/commit/1230df15e47298961dfaf8bdf0dc519d317e064f>`_.

Updating ``build.gradle``
=========================

1. Near the top of your ``build.gradle`` file, change the ``springBootVersion`` parameter to the value ``'2.1.1.RELEASE'``.
2. Near the middle of the file, add the following plugin to the list of applied plugins: ``apply plugin: 'io.spring.dependency-management'``
3. If you have a ``bootRun`` block (most likely at the bottom of your file), delete it.

Updating Gradle
===============

Spring Boot 2 requires Gradle version 4.4 or newer.

Open the file ``gradle/wrapper/gradle-wrapper.properties`` and find the line that defines ``distributionUrl``. If URL references an older version of Gradle, update it: ::

    distributionUrl=https\://services.gradle.org/distributions/gradle-4.4-bin.zip

.. tip:: You can check your work up to this point by opening the *Gradle* pane in IntelliJ and hitting the *Refresh All Gradle Builds* button (at top-left). Then open the *Build* pane and make sure there are no errors.

Using New Repository Methods
===============================

From the *Gradle* pane, select *Tasks > build > build* by double-clicking. This will build your project, flagging any compiler errors.

In particular, you will see quite a few errors relating to your Spring Data repository interfaces. This is due to some `interface method name changes <https://spring.io/blog/2017/06/20/a-preview-on-spring-data-kay#improved-naming-for-crud-repository-methods>`_ in Spring Data (part of the "Kay" release of Spring Data). We have been using the ``JpaRepository`` interface, which extends the ``CrudRepository`` interface``. Both of these interfaces have updated methods, which we will need update our code to use.

We will walk through the changes that affect the ``launchcart`` project, and which are most common. If you are upgrading another project you may find other method name changes. Refer documentation for `JpaRepository <https://docs.spring.io/spring-data/jpa/docs/2.0.0.RELEASE/api/org/springframework/data/jpa/repository/JpaRepository.html>`_ and `CrudRepository <https://docs.spring.io/spring-data/commons/docs/2.0.0.RELEASE/api/org/springframework/data/repository/CrudRepository.html>`_, as well as the `Spring Data Kay Release blog post <https://spring.io/blog/2017/10/02/spring-data-release-train-kay-goes-ga>`_.

.. tip:: To carry out the updates detailed below, we recommend:
    1. Reading all three subsections below *before* changing your code, to understand the necessary changes.
    2. Referring to the error messages displayed in the *Build* pane, click through the errors one-by-one to update your code.

``JpaRepository.getOne``
------------------------

In ``JpaRepository``, ``findOne`` has been renamed ``getOne``. In all controllers and tests calling ``JpaRepository.findOne``, update the method calls to ``getOne``.

``CrudRepository.deleteById``
-----------------------------

In earlier versions of Spring Data, the method ``CrudRepository.delete`` took the ID of an entity to delete. It not takes the object to delete itself. Thankfully, there is now a ``CrudRepository.deleteById`` method that takes an ID and deletes the corresponding item.

Update all usages of ``CrudRepository.delete`` to ``deleteById``.

``CrudRepository.existsById``
-----------------------------

Previously, ``JpaRepository.findOne`` would return ``null`` when given the ID of a non-existant item. The caller could then check for ``null`` to determine whether or not an item with the given ID existed.

With the new Spring Data release, ``JpaRepository.findOne`` was renamed ``JpaRepository.getOne``, and the new method has the behavior that it throws an exception if an item with the given ID does not exist.

In situations where ``JpaRepository.findOne`` was used in conjunction with a ``null`` check (such as ``ItemRestControllerTests.testDeleteItem`` and the ``DELETE`` method of ``ItemRestController``), update your code to use the new ``existsById`` method.

.. tip:: To check your work up to this point, re-run the ``build`` task to ensure the build successfully completes.

Updating ``application.properties``
===================================

At this point, if you run the ``bootRun`` task you will see an exception in the console, ``java.lang.reflect.InvocationTargetException`` (try it!). While this exception looks scary, it is actually harmless, as the status of the associated log message just above it (``INFO``) indicates.

To suppress the check that generates this exception, add the following line to ``application.properties``:

::

    spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true

Test Your App
=============

Hopefully your app is fully upgraded and ready to go. To be sure, run all of your tests, and then start the app using the ``bootRun`` task. If you encounter any errors, review the steps above and/or the links below for help.

Resources
=========

- `Spring Boot 2.0 Release Notes <https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Release-Notes>`_
- `Spring Boot 2.0 Migration Guide <https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide>`_
- `Spring Data Kay - Improved naming for CRUD repository methods <https://spring.io/blog/2017/06/20/a-preview-on-spring-data-kay#improved-naming-for-crud-repository-methods>`_
