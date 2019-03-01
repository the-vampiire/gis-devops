:orphan:

.. _tdd-walkthrough:

================
Walkthrough: TDD
================


Setup
-----

* We will use the same repo from Day 1
* Open the car unit tests project in Intellij https://gitlab.com/LaunchCodeTraining/car-unit-tests-starter
* From your solution branch from yesterday create a new branch for today's solution

Follow Along
------------

1. Discuss the requirements we want to add
2. Create a test list
3. Write the tests
4. Make them pass
5. Clean up the code if there are any code smells

Discuss Requirements
____________________

Following TDD and the red-green-refactor workflow we need to understand our requirements before writing any code.

Today we will be implementing Range, and Trip Meter functionality.

The Range functionality will calculate how many more miles the car can drive before running out of gas. The output should be a Double reprsenting the miles the car can drive before needing to fuel up.

The Trip Meter will be a separate resetable odometer. This will allow a user to track the number of miles travelled on a specific trip.

Create a Test List
____________________

Range

* testInitialValue
* testValueAfterDriving
* testValueAtEmpty

Trip Meter

* testInitialValue
* testAfterDriving
* testResetAfterDriving
* testResetThenDrive

Write the Tests
_______________

Range

.. image:: /_static/images/tdd/tdd-range-tests.png

Trip Meter

.. image:: /_static/images/tdd/tdd-trip-meter-tests.png

Our tests won't run yet, because our code won't compile. We are calling a few methods that don't exist. We will need to create these methods to get them to run.

.. image:: /_static/images/tdd/tdd-additional-methods.png

Make Them Pass
______________

Now upon running our tests, a couple of them pass. However we still need to update some code in our Car.java file to pass the rest of our tests.

.. image:: /_static/images/tdd/tdd-some-passing-tests.png

Our tests are currently failing every time our car drives. We need to add some code to our drive method so that it updates the TripMeter as well as the Odometer.

.. image:: /_static/images/tdd/tdd-update-drive-method.png

Now when we run our tests, they all pass!

.. image:: /_static/images/tdd/tdd-successful-tests.png

Clean Up Code
_____________

Now that are tests pass, it's a good idea to clean up our code. Our project is pretty small still, but there are a lot of redundancies in our CarTest.java file. We should clean them up.

We will use @Before, and @After to set some variables we can use in each of our tests, so we don't have to declare a new object at the start of every test. This will cut down on our redundant code.

.. image:: /_static/images/tdd/tdd-atbefore.png

Using the @Before annotation allows us to run some code before every test method in this class. In this example we use the @Before annotation at the top of our Class, and we are setting up a setup_car. We will then change our tests to test this car, instead of requiring each of our tests to create a new car object.

.. image:: /_static/images/tdd/tdd-refactor-tests.png

We can now change our test methods to change, and make assert statements the setup_car. Look over your other tests, and see if they can benefit using the @Before annotation.

After refactoring your code, re-run your tests to make sure they all pass. If they don't make the changes necessary so they will pass. Then it would be a great idea to push your work to your personal GitLab account.
