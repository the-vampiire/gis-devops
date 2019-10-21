:orphan:

.. _week4-new-data:

===============================
Refactor to Allow for New Data!
===============================

The underlying data for our web application has changed, and we have new requirements!

The first thing we need to do before working on our new features, is to get our new data, understand, and make changes to our web application, and data stores to allow for this new change in data.

Tasks
    - Download new data
    - Understand new data
    - Setup our PostGIS container
    - Amend our classes to match data
    - Use Hibernate to create tables
    - Amend import.sql to populate the new tables
    - Check our database for records


Download New Data
-----------------

You can find our new data in the `week4 branch of the zika-data <https://gitlab.com/LaunchCodeTraining/zika-data/tree/week4>`_ repository we used last week.

We recommend overwriting the CSV files you currently have in your zika-data folder in your project with these new files. Don't worry, you won't lose the old files because they are still recorded in your remote repository (GitLab)!

Understand New Data
-------------------

Whenever you have a change in data you should always try to understand the changes, and how it will affect your project. We may need to change things, delete thing, add new things just to accomidate a change in data. Outlining these changes is a great way to keep yourself organized as you work to incorporate the changes.

The first thing that jumps out is that we no longer have CSV files for each Country, but instead have all of our Zika report data in one CSV file called ``all_reports.csv``. We will eventually have to change our ``import.sql`` file since we no longer are reading from 5 different CSVs for our report data.

You will also notice that we have a ``locations.csv`` file, and instead of using geopoints, we are using multipolygons! Instead of representing a zika record as a point, we will now represent a zika record in a state within a country. The multipolygons represent states/provinces/territories of various countries. Our report data, and our location data are in separate files, and will live in separate tables of our database. We will have to join the report data with our location data in order to display them on our map. We will eventually have to change code in our controller files to combine this information when the ``script.js`` file requests GEOJSON.

Before we can change the files that load this data, we will need to make sure our database, underlying classes, and sql files are ready for this change in data!

Setup POSTGIS Container
-----------------------

One of the many benefits of using a containerization tool like Docker is we can easily control our containers. We currently have a PostGIS container for airwaze, and the zika project we worked on in week2. Instead of using the same container from week 2 we are going to setup a new PostGIS container for week4. This allows us to move quickly between different versions of our project. If you ever want to go back and see week2 in action you can simply turn off the week4 container, and turn on the week2 container.

However, our operating system will only allow one application to listen on one port at a time, so we first need to turn off any containers that are currently listening on port 5432.

From your terminal run ``$ docker ps`` to check which containers are currently running.

If you have any containers running go ahead and turn them off. You can do that by running ``$ docker stop [CONTAINER_NAME or CONTAINER_ID]`` from your terminal.

Now we want to create a new container with PostGIS. We've done this a couple of times throughout this class, but it's always nice to have a reminder. Look over :ref:`docker-postgis` for assistance.

Like last week you will need to create 2 databases, and 2 users for your databases.

Amend Classes to Match Data
---------------------------

Now that we have a shiny new PostGIS database, we need to amend and create our classes to match our data.

From last week we have a Report class already, but it doesn't quite match our new data. You will need to amend the ``Report.java`` class so that it matches the reports in ``all_reports.csv``. You will need to remove anything about Geometries, and you may need to remove more.

We will also need to create a new Location class. Create a new class called ``Location.java`` in your models directory. The properties of this class should match the headers of the ``locations.csv`` file.

Use Hibernate to Create Tables
------------------------------

We now want Hibernate to create tables in our database to match our Report class, and Location class.

We don't want to load anything in to our tables yet, we just want to create the tables. So you will need to delete, or comment out your ``import.sql``. You can comment out lines in SQL by adding ``--`` before the line. Comment out, or clear all the lines now.

Check your ``application.properties`` file for the ``spring.jpa.hibernate.ddl-auto`` option. What do you need to set this variable to to have Hibernate create our tables for us?

After setting ``spring.jpa.hibernate.ddl-auto`` to an appropriate value run your project with ``bootRun``. You should see the Tomcat logs spit out some lines about creating new the new tables.

It would be a good idea to login to your PostGIS container, and check that the two tables were created correctly.

.. hint::
   
   If you get an error when you run bootRun it is probably because one of your controllers calls the ``.getGeom()`` method which no longer exists after we changed our ``Report.java`` class. Comment out, or remove this code for now. We will come back to it later. You may also have other errors, work through them by following the code, and working with fellow classmates.

Amend import.sql to Populate Tables
-----------------------------------

Now that we have our database setup correctly we need to change the ``import.sql`` file. 

Like last week we will need to use ``$ docker cp`` to copy our CSV files to our docker container.

From last week this file is loading from the 5 CSV files into 1 table. Now we want to load from 2 CSV files into 2 different tables. Similar to last week you will have to match up the CSV headers to the columns in the table.

.. note::
   
   Don't forget about ``spring.jpa.hibernate.ddl-auto`` inside your ``application.properties`` file! If you don't have that property set correctly, the ``import.sql`` file won't run!

At the bottom of this file before ``COMMIT;`` add 3 additional lines.

.. sourcecode: SQL
   
   CREATE EXTENSION IF NOT EXISTS unaccent;
   UPDATE location SET name_0_normalized = unaccent(name_0);
   UPDATE location SET name_1_normalized = unaccent(name_1);

These three lines load the unaccent extension to PostGIS, and then normalize the Country name, and State name of each report. Unaccent removes any diactrics, or symbols from letters. This will make our searches more effective in later objectives.

Check our Database for Records
------------------------------

After you amend ``import.sql`` re-run your project with ``bootRun`` make sure the Tomcat log is free of errors, and then log back into PostGIS from the command line and count the number of records in each table. We are expecting 250 location records, and 240,272 report records.

You can count the number of records from inside PostGIS with ``SELECT COUNT(*) FROM report;`` and ``SELECT COUNT(*) FROM location;``.

Fix Broken Tests
----------------

Since our data has changed, and how our application handles the data has changed our tests are no longer correct.

Once you get to this point we should run our tests. Make sure to update your ``application-test.properties`` and ``import-test.sql`` files and then run your tests. You will notice that a large number of them fail. Since we have changed how our application handles the data, most of our tests are obsolete and will need to be refactored, or scrapped and re-written.

Run your tests, and either fix, or delete them. If you delete them you will need to create new tests later as you work through the remaining objectives.

Closing remarks & next steps
----------------------------

Congrats! We now have a new database with 2 tables, report, and location. We have also updated the classes of our spring application to match, and we populated our new tables with the correct records. We have updated our database, and the models of our project. We still have a lot of work to do, but we have passed a significant step for this week!
