:orphan:

.. _walkthrough-unitTest:

=======================
Walkthrough: Unit Tests
=======================


Concept: Unit Testing
---------------------

A unit is a single, clearly state behavior. In many cases it will be one funciton, or method.

A unit test is an automated test that verifies that one unit behaves correctly.

We will be writing unit tests using JUnit 4 in IntelliJ. Some example code has been provided for us. We will be writing unit tests for the code that currently exists.


Fork, Clone, and Open Project in IntelliJ
-----------------------------------------

* `Fork this repo <https://gitlab.com/LaunchCodeTraining/car-unit-tests-starter>`_
* Clone your forked version of the repo locally
* Create a new branch for the new unit tests we will be writing ``$ git checkout -b my-unit-test-solution``
* Open IntelliJ
* Choose **open folder** and navigate to and choose folder **car-unit-test-starter**


Configure IntelliJ for Java 8
-----------------------------

* Open the Project Structure

.. image:: /_static/images/unit-tests/project-structure.png

* Set our Project SDK

.. image:: /_static/images/unit-tests/project-sdk.png

* Set our Project language level

.. image:: /_static/images/unit-tests/project-language-level.png

* Create a new folder for our compiler output

.. image:: /_static/images/unit-tests/new-out-folder-location.png

.. image:: /_static/images/unit-tests/new-out-folder.png

* Set our Project compiler output

.. image:: /_static/images/unit-tests/project-compiler-output.png

* Mark the main directory as Source Root

.. image:: /_static/images/unit-tests/main-directory-as-source-root.png

* Mark the test directory as Test Sources Root

.. image:: /_static/images/unit-tests/test-directory-as-test-root.png

Add an Empty Test
-----------------

We need to create an empty test so that we can create our testing runtime configuration. However, IntelliJ will only find our test class if it has a valid test in the class body.

* In CarTest.java add an empty test

.. image:: /_static/images/unit-tests/empty-test.png

* Add JUnit 4 to your class path (click @Test and then option+enter)

.. image:: /_static/images/unit-tests/junit4-classpath.png

* Import Test, and AssertEquals

.. image:: /_static/images/unit-tests/import-assertequals.png

Create a New Runtime Configuration
---------------------------------- 
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

* View test results 

.. image:: /_static/images/unit-tests/first-test-results.png

Currently our one test passes, because it is testing if 10 is the same value as 10 which is true.

Write and Run our Unit Tests
----------------------------
* Write tests

.. image:: /_static/images/unit-tests/completed-tests.png

Our first three tests are very similar to one another. They each have a test_car object, and are checking that the gas tank level is correct after instantiating the car, driving the car within the range, and driving the car exceeding the range.

The assertEquals() method takes three parameters: The value we want to test, What it's value should be, and the delta (how much the actual, and expected values can differ and still return true).

Our fourth test is a little different. We are going to perform an action to our car object, and we are expecting the object to throw an error. In this case we are going to attempt to add gas to our car that exceeds the gas tank size.

.. image:: /_static/images/unit-tests/test-expected-exception.png

We are able to expect an exception to be thrown by adding (expected = IllegalArgumentException.class) after our @Test annotation. This is our way of telling Junit 4 that this test should pass if an IllegalArgumentException is thrown at any point during this test.

We also are calling the addGas method which has not yet been added to the Car class, we will need to add this functionality to run our test.

The fail() message will be displayed if the test fails. We have to import the fail method into this class to use it.

Let's add the addGas method to our Car class so we can finish this final test.

* Add the addGas method to Car.java

.. image:: /_static/images/unit-tests/test-add-gas.png

* Run your tests

.. image:: /_static/images/unit-tests/failed-car-test.png

My new test failed! In the output I got an unexpected exception. This test was expecting an IllegalArgumentException, but it got an AssertionError execption. This caused my test to fail. Further down in the output log we can see that our fail() statement printed out the statement about not being able to add more gas to the gastank than is possible.

* Refactor our Car class to throw an exception when too much gas is added to the gas tank

.. image:: /_static/images/unit-tests/test-set-gastank-level.png

We added some code to our setGasTankLevel method that checks if the new gas tank level is greater than the gas tank size, and if it is it throws an IllegalArgumentException.

* Rerun our tests

.. image:: /_static/images/unit-tests/passed-car-test.png

This time our test passed, and nothing was printed out to the output log! We have successfully written 4 unit tests in this walkthrough!

.. hint::

  You can open a TODO window by going to **View** then **Tool Windows** then seelct **TODO** this opens a box at the bottom of IntelliJ showing you all the TODOs in the project.


Resources
---------
* `JUnit 4 Site <http://junit.org/junit4/>`_
* `Examples of Assertions <ttps://github.com/junit-team/junit4/wiki/Assertions>`_
