:orphan:

.. _week2_display-reports:

==========================================
Project Week2: Display Zika Reports on Map
==========================================

We have an empty interactive map from OSM.

We need to create a new Layer in OpenLayers that will represent our Zika report data.

This will require a fair amount of work, luckily it is primarly things we did in our `Airwaze project <../../studios/airwaze/>`_.

Tasks:
    - Design Report.java class
    - Create ReportTests.java
    - Report table is populated from CSVs
    - Create ReportTests.java
    - Create ReportController.java
    - Create ReportControllerTests.java
    - Create ReportRepository.java
    - Create ReportRepositoryTests.java
    - Amend script.js
    - Verify Zika report layer renders
    - Commit

Design Report.java Class
------------------------

Before we display our report data as a layer on our map in OSM we will first have to transform our data into a state we can work with.

Ultimately OpenLayers is expecting a source of data in a GeoJSON format. Right now our data is locked away in various CSV files. You can find the files you will need in the `zika-data repository <https://gitlab.com/LaunchCodeTraining/zika-data>`_ on GitLab.

Let's consider the journey of our data.
    1. Report data exists as 6 CSV files
    2. CSV files are copied as records in a report table in a PostGIS database
    3. JpaRepository loads records from PostGIS and stores them as Report objects
    4. Report objects are converted into GeoJSON
    5. OpenLayers consumes GeoJSON to create a visual layer on a map

In order to COPY our CSVs into a PostGIS table, the table must exist, and it's columns must match the SQL COPY statement. We have been using Hibernate to create our PostGIS tables for us, Spring scans our project for the @Entity annotation, and automatically creates a table based on the properties of that class. In order to create our table we will first need to design our Report class!

Look over the `provided CSV files <https://gitlab.com/LaunchCodeTraining/zika-data>`_, look for their **shared** columns. These shared columns will be the columns in our table, and we need to create properties on our Report class to match these columns.

Look at your solution to the `Airwaze Studio <../../studios/airwaze/>`_. Take note of how the ``Airports.csv`` file matches up with the ``models/Airport.java`` file, and how the ``routes.csv`` file matches up with the ``models/Route.java`` file.

.. hint::

   Hibernate uses the `snake case <https://en.wikipedia.org/wiki/Snake_case>`_ naming strategy. You can explicitlly define a Column name by using the Spring Data @Column annotation. Above the property declaration you can include the @Column(name = "columnName") annotation and Hibernate will name the column based on the value mapped to the name variable.

After you create your Report.java class re-run ``bootRun``, and then check your database. Your report table still won't have any records in it, but you should see a report table with columns that match the properties of your Report class.

Create ReportTests.java
-----------------------

When creating a new class in Java we should test this file.

Create ``ReportTests.java`` in your tests directory. Look over the `Unit Testing walkthrough <../../walkthroughs/unit-tests/>`_ if you need help writing these tests.

We advise creating a ``testReportConstructor()`` method that will create a new ``testReport`` object based on information  from one of the CSV files. This will allow you to perform ``assertEquals`` statements to verify the ``testReport`` object contains information that matches our CSV file. 

.. note::
   
   In most cases people will not test constructors in Java, or any language. An object constructor is code handled by the team responsbile for the programming language itself, and you typically don't test code written by third parties. The purpose of this class is learning, and practice. So we will be creating a test case for our constructor.

Populate Report table
---------------------

Now that we have a tested Report class, which created the correct structure for our report table, we want to populate the table with the data from our CSV files.

In our ``application.properties`` file we have this line: ``spring.jpa.hibernate.ddl-auto=create``. This tells our spring project to create any tables by finding the ``@Entity`` annotations, and it tells our spring project to run the ``import.sql`` file found in our resources folder. We can include our COPY statement inside this SQL file and our records will be added to the database when our project starts up!

You are going to have to create your own COPY statement. Look at the ``import.sql`` file in your solution to the `Airwaze studio <../../studios/airwaze/>`_ to see how they crafted the COPY statement.

The SQL COPY statement includes:
    - COPY - the statement we are running in SQL
    - table_name - the table we are copying records into
    - (column_name1, column_name2, column_name3, etc) - the columns the data in the CSV file is being mapped to
    - from ``/path/to/file.csv`` - the CSV file we are copying data from
    - DELIMITER ',' - the delimiter for this CSV file is a comma
    - CSV HEADER - this file includes a header row as the first row and it should be ignored when copying records

We are using a PostGIS Docker container, so the path to your file will be ``/tmp/filename.csv``. And you will have to copy the CSV files to this location.

You can copy a file to your docker container from the command line with the following command.

.. sourcecode:: console
   :caption: Docker cp
   
   docker cp <file_to_copy> <container>:/tmp

This command will copy your file to the /tmp folder in your docker container. You will need to do this for each file you need to copy to the report table!

After you are done with this, you should re-run ``bootRun`` to make sure it copies the records into the database correctly. It would also be a good time to review the options for ``spring.jpa.hibernate.ddl-auto``. The final section of `this Baeldung article <https://www.baeldung.com/spring-boot-data-sql-and-schema-sql>`_ gives some information on the differences between the options.

Create ReportRepository.java
----------------------------

Now that we have data in our database, we need a way to interact with the data from our web application. We can create, and copy records into the database, but we will have to add some additional code to access, or amend the records in the database.

We need to create a ``ReportRepository.java`` class. It is a good idea to store this class inside of it's own folder we recommend using a ``org/launchcode/zikaDashboard/data`` folder. Separating your data Repositories from your models, and controllers is a way to stay organzied, and will help other coders understand your project.

Our new repository class will need to extend JpaRepository. Look at the ``AirportRepository.java`` class from the `airwaze studio <../../studios/airwaze/>`_ for help on setting up this class.

Right now we are interested in basic CRUD functionality, and don't need to add any additional methods. However, later in this project week you may need to add methods to find specific reports based on information about that report.

Create ReportRepositoryTests.java
---------------------------------

In creating a new JpaRepository we need to write some tests as well. Look at the ``AirportRepositoryTest.java`` file from the `airwaze studio <../../studios/airwaze/>`_ for help.

We advise creating a ``testGetAllReports()`` method that uses ``reportRepository`` to load all reports into a list. You can then assert if the length of that list matches the number of records in your database. You can also pull out one of the reports and assert that the data matches.

Create ReportController.java
----------------------------

We have a Report class, a report table with records, and a ReportRepository to manage the flow of information between Spring, and the database. Now we need some way of handling an HTTP request from OpenLayers, and the response needs to include a GeoJSON representation of our report data.

As we have learned from this class the ``@Controller`` annotation allows us to handle HTTP requests, and serve up HTTP responses. We will need a controller, an endpoint, and some logic to get the information from the database, turn it into GeoJSON, and then package it into an HTTP response.

Again, as a good practice we should store all of our controlles, in their own directory called ``controllers/``. Create this new directory, and add a new file called ``ReportController.java``.

In this file you will need to setup a new method handler for an endpoint that will return a GeoJSON representation of our report objects.

Convert Java Object to GeoJSON
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Look over the ``AirportController.java`` file from the `Airwaze code base <https://gitlab.com/LaunchCodeTraining/airwaze-studio/blob/master/src/main/java/com/launchcode/gisdevops/controllers/AirportController.java>`_.

You will note in this file the ``getAirports()`` request handler method is returning a FeatureCollection object. The FeatureCollection is the GeoJSON! Look over both the `FeatureCollection.java <https://gitlab.com/LaunchCodeTraining/airwaze-studio/blob/master/src/main/java/com/launchcode/gisdevops/features/FeatureCollection.java>`_ and `Feature.java <https://gitlab.com/LaunchCodeTraining/airwaze-studio/blob/master/src/main/java/com/launchcode/gisdevops/features/Feature.java>`_ files.

You will need to recreate both of these files in your project in order to return our report data as GeoJSON. A Featue is one record in GeoJSON. A FeatureCollection is multiple Features together, still in GeoJSON.

Notice that the Feature, and FeatureCollection classes are using a new annotation we haven't seen yet: ``@JsonSerialize(using = GeometrySerializer.class)``. The JsonSerializer takes our object and converts it into JSON. To convert our GeoINT information we will also need ``GeoJSONSerializer.java``, and ``WktHelper.java``.

All four of these files are how we are converting our Java Report objects into GeoJSON that is usable by JavaScript.

In the handler method you will need to load all of youre reports from the ReportRepository, loop through each report, create a new Feature from each report, and add each Feature to the FeatureCollection, and finally return the FeatureCollection as a part of the HTTP response.

Create ReportControllerTests.java
---------------------------------

Since we are creating a new class we also need to test the class. Look at the ``AirportControllerTest.java`` file from the `airwaze studio <../../studios/airwaze/>`_ for help.

We advise creating a ``testGetReports()`` method that will use MockMvc to perform a get request to the endpoint you created in your controller. You can expect the HTTP Status to be 200, and you should expect something about the number of reports, or something inside one of the reports.

Amend script.js
---------------

Now that we have taken our data from CSV to a report table to Report objects to GeoJSON, we can finally create a new Layer in OpenLayers.

You will need to create a new reportLayer, and add it to your map.

Look over the ``script.js`` file from the `airwaze studio <../../studios/airwaze/>`_ for help.

Verify Zika report layer renders
--------------------------------

And finally, re-run your project, and make sure your Layer is displaying properly.

.. hint::

   Make sure you have created a style for your Report Layer. The browser can't render the Layer if it doesn't know what the Layer should look like.

Commit
------

After your tests pass, and you have manually checked your project commit and push your work!

`Back to week2! <../project/>`_