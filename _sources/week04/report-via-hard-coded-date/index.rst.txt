:orphan:

.. _week4-report-via-hard-coded-date:

=========================================
Display Report Data via a Hard Coded Date
=========================================

Now that we have updated our project to work with our new code, let's get our map back to displaying data. It's currently a mess.

Our map still loads, but it's making a request to ``/report`` and that controller goes to our report database, and tries to load everything into a Feature Collection (GeoJSON) to load as a new report layer.

Now that our data has changed by separating our report data, and location data, we will need some way to join the data together before it becomes a Feature Collection (GeoJSON).

On top of that, we have a new feature request from our stakeholders to display reports based on time. We are going to need a way to load reports based on one date, instead of loading all the data at once.

REST to the Rescue!

We are going to create a new ReSTful endpoint the user can make a request to including the date as a query parameter. We will load all reports that match our string_date query parameter, join the location data to the reports, and then return a shiny new Feature Collection (GeoJSON) that our map can understand!

It's going to take a little work, so let's break down our tasks:
    - Create a ``ReportRestControllerTests.java`` file
    - Create a ``ReportRestController.java`` file
    - Amend ``ReportRepository.java``
    - Amend ``script.js``

Create ``ReportRestControllerTests.java``
-----------------------------------------

Following TDD is something we try to do in this class. Let's write our test for our new controller first.

Create a new file in your tests directory called ``ReportRestControllerTests.java``.

We haven't defined what endpoint, but will need one for our test file. Following ReST principles the endpoint should be descriptive. ``/api/reports`` is a good choice, just remember that your endpoint must be unique in order for Spring to register it correctly. We also need to pass in a string date as a search parameter.

This file should at least test for an HTTP Response status of 200, but seeing as we are testing a Rest Controller we should also test the JSON included with the HTTP Response.

Take some time to really consider what goes in this test. What should be returned from our RestController when a user makes a request with the parameter: ``?date=2016-03-05``? What should be returned? How do we check for that in our test file?

To expand your test coverage, what should be returned when the user requests a date that doesn't have report data: ``?date=1802-15-36``?

For assistance look over :ref:`walkthrough-launchcart-rest`. Use this walkthrough to guide the tests you are writing for you RestController.

Amend ``ReportRepository.java``
-------------------------------

Our JpaRepository classes give us CRUD functionality over our PostGIS database. We created a ``ReportRepository.java`` class last project week, to get reports from our database.

We changed our underlying data, but our current ``ReportRepository.java`` class still works. However, we need to extend the functionality of this class. We need to only fetch records that match a specfic string date.

You will need to add a method to this class to allow for this new functionality.

Look over the `ItemRepository.java class <https://gitlab.com/LaunchCodeTraining/launchcart/blob/rest-walkthrough-solution/src/main/java/org/launchcode/launchcart/data/ItemRepository.java>`_ from the LaunchCart application we worked on earlier this week for assistance.

Create ``LocationRepository.java``
----------------------------------

Another difference this week is that we have separated our report, and location data. Before we could simply create a new FeatureCollection (GeoJSON) from our report data because it included the location data. Now we are going to have to manually join these things together.

To do this we are going to need a new JpaRepository attached to the location table of our database. You can do this by creating ``LocationRepository.java``. Model it after your ``ReportRepository.java`` class.

We are going to need to extend the functionality of this class, to find locations based on something from our report data. If you aren't sure yet on how to do that, feel free to move on and re-address it when you need it in the next section.

.. note::

   We are creating a new JpaRepository, and creating a custom method. This is something we should test. Don't forget to create a new test file for this repository. It may be difficult to create the test if you are still piecing together how this objective works. So feel free to come back to the test later if you need too.

Create ``ReportRestController.java``
------------------------------------

Now that we have defined what HTTP Reponse should be returned when a users makes an HTTP Request to our ReSTful endpoint. We can write the code for our controller.

You may have an idea of what you need to do in your controller, if so go for it! If you are struggling look over :ref:`walkthrough-launchcart-rest` and your solution to :ref:`launchcart-rest-studio`.

As a reference to check your work make sure that you:
    - Create a new file: ``ReportRestController.java``
    - Use the ``@RestController`` annotation
    - Create a new endpoint handler for our request
    - Parse any query parameters
    - Get data from the database using ReportRepository and LocationRepository
    - Join the report data, and location data together in a Feature
    - Add the Feature to the FeatureCollection
    - Return the FeatureCollection

Amend ``script.js``
-------------------

Now that we have an endpoint ready to serve GeoJSON we can change our ``script.js`` file.

Last project week we were building a new report layer by making a request to an endpoint. We will need to update this request to use the new endpoint we just defined!

Make sure to include a query parameter in your request. We recommend using ``2016-03-05`` to test your data. However, you should also look into the report data and pick some other dates to manually test.

Congrats on finishing this step. We had to make a lot of changes to get this far.

Our web application is now handling requests that include dates. We still need to provide a way for users to change the dates to explore the data and will get to that in our next objective!