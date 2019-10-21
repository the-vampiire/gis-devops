:orphan:

.. _runtime-configurations:

===============================================
Configuration: IntelliJ - Runtime Configuration
===============================================

After we write our code, we still need to run our code to verify it works. Traditionally with Java project's you would need to compile, and then run the correct class file all from the command line. Luckily IntelliJ provides us with the tools to compile and run our project without needing to leave the IDE.

IntelliJ does this with Run/Debug Configurations. In a Run/Debug Configuration we can decide what tool we will use to build the application and which class is run.

We will be able to create Run/Debug Configurations for standalone Java applicatoins, Gradle commands, JUnit commands, etc. When using Gradle later in this class, when we run bootRun it automatically creates a Run/Debug Configuration for us, but we still have the right to go in and configure it to fit our needs.

General Steps
-------------

Every Run/Debug Configuration is a little different, but for the most part they need to have a few things setup before they will work.

1. From the Run/Debug Configuration Screen click the + (plus) sign to add a new configuration
2. Select the type of Configruation throughout this class we will primarily select JUnit or Gradle.
3. Name the configuration
4. Select Classpath of Module (main for applications, test for JUnit)
5. Select the Class (main, or a JUnit test class)
6. Input Environment variables if necessary

Example
-------

In this example we will be creating a new JUnit configuration for `car-unit-tests-starter <https://gitlab.com/LaunchCodeTraining/car-unit-tests-starter>`_

* Click Add configuration

.. image:: /_static/images/unit-tests/add-configuration.png

* Click the + icon and select JUnit

.. image:: /_static/images/unit-tests/plus-junit.png

* Name the configuration
* Select the class file where our tests live
* Apply the configuration

.. image:: /_static/images/unit-tests/name-class-apply-configuration.png

* Run new configuration by clicking the green arrow

.. image:: /_static/images/unit-tests/run-test-configuration.png

And we are done! In this case we didn't need to add any environment variables. Also the test module was automatically detected as the Classpath of Module so we didn't even need to manually select it.