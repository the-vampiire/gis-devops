.. _week4_project:

===================================================
Week 4 - Project Week: Zika Mission Control Part 2
===================================================

Project
=======

`Mission Briefing 2 <../../_static/images/zika_mission_briefing_2.pdf>`_

Overview
========

Your goal is to extend the CDC Zika Dashboard that you built last week. The scientists need several new features for the dashboard. Here are the stories:

1. CDC scientists have the ability to add new reports to the database.
2. CDC scientists have the  ability to visualize how the infection rate of each area changes over time.
3. CDC scientists have the ability to perform fuzzy search across all of the data.

.. note::

  Remember, in Agile a story is just a "guaranteed conversation". Stories usually don't contain all of the details necessary to complete the task and that's why it is important to follow up with the client and talk through the exact needs of your customer.

Getting the code
================
1. Create a story branch named ``week4-solution`` from your solution to the week 2 zika project
2. Make sure you have a remote in your repo that points to https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard
3. Run ``git fetch upstream`` (Assuming upstream points to the above repo)
4. Run ``git checkout -b week4-starter upstream/week4-starter``
5. Run ``git checkout week4-soltuion``
6. Now you can get the new files by pulling them in. Run ``git merge week4-starter`` OR by simply pasting in select files. The choice is up to you.

.. note::

  If you decide to back out of a merge and try something else you can always reset your branch to it's previous state.
  The below command destroys all non committed changes in your local branch and reverts back to the previous commit. ``$ git reset --hard``

What's new in the code
======================

* CSV files ``data/locations.csv`` and ``data/all_reports.csv``
* ``src/main/resources/import.sql`` copies the csv data into PostGIS when SpringBoot starts up (as long as `spring.jpa.hibernate.ddl-auto` is `create` or `create-drop` in `application.properties`)
* Location data now contains multi-polygons instead of a single point
* Elasticsearch dependencies and Repositories have been added
* ``ESController`` contains an endpoint to populate Elasticsearch with all data in your PostGIS db

Requirements
============

Use TDD when implementing these requirements

1. Build out a ``/api/report`` endpoint that accepts a POST containing Report JSON in the body.

   * Store the ``Report`` created from JSON in PostGIS
   * Store the ``ReportDocument`` created from JSON in Elasticsearch

2. Show Zika report data for a certain date on a map via OpenLayers (reports grouped by state for a certain date)
3. When a feature is clicked show the related report data (like in week 2 zika project)
4. Ability to change the data displayed by changing the report date
5. Search input and search button that uses Elasticsearch fuzzy search

   * When search is executed matching reports should be shown below map
   * And/Or Features present on map should change to be only those that match report date and fuzzy search term

6. Make sure you application is secure from `SQL injection attacks <https://www.owasp.org/index.php/SQL_Injection>`_ by validating the query parameters.
7. Create API docs with Springfox.
8. Use Eslint and Airbnb ruleset to make sure your JS meets team standards.

Suggested Endpoints/Parameters
==============================

1. ``api/report?date=2016-05-14`` should return GeoJSON created from reports filtered by report date

   * Most likey you want this data to come from Elasticsearch because of requirement #2

2. ``api/report?search=brzil`` should return GeoJSON created from Elasticsearch filtered using fuzzy search
3. ``api/report?search=brzil&date=2016-05-14`` should use both the ``date`` and ``search`` query parameters to limit the results.
4. ``api/report/unique-dates`` returns JSON containing all unique report dates

.. note::

    To index all of the reports in PostGIS into Elasticsearch use the following command: ``$ curl -XPOST http://localhost:8080/api/_cluster/reindex``

ReportDocument Structure
------------------------

You'll need to create a ``ReportDocument`` class to model ``Report`` data to be stored in Elasticsearch. There are a few things to keep in mind when doing so:

* The ``ReportDocument`` class should have an ``id`` proprty of type ``String``, annotated with ``@Id``. For a given ``Report`` object, the corresponding ``ReportDocument`` *will not* have the same identifier, since ES documents have hashes as identifiers. You should keep track of the ``Report`` object's identifier in a new field named ``reportId`` in ``ReportDocument``.
* The ``dataField`` values of ``Report`` objects look like this: ``gbs_reported_zika_confirmed``. For better searching within ES, it's necessary to convert underscores to spaces. Do this in the ``ReportDocument`` constructor when setting the ``dataField`` value.
* Similarly, it will be easier to search by date if we store a new field ``stringDate`` on our ``ReportDocument``. Use the ``SimpleDateFormat`` class to create a string representing the report's ``date`` field, in the format ``yyyy-MM-dd``. Similarly, you'll want a ``findByStringDate`` method in your ``ReportDocumentReporistory`` for searching by date.

Database Setup
==============

Install the following extension on your PostGIS databases (don't forget to do the same in your test db):::

  # CREATE EXTENSION unaccent;

Walkthrough on Creating the Location Data
=========================================

All the spatial data you need is already included in the starter branch. However before starting to code this project please go through this walkthrough to see how it was created.  This will provide some insight into creating and configuring spatial data such as country and state boundaries.

Adding Boundary Geometries
--------------------------

The data that the scientists want to ingest is summarized in the `CDC Zika Repository <https://github.com/cdcepi/zika>`_. If you open up the `data for Argentina <https://github.com/cdcepi/zika/blob/master/Argentina/Surveillance_Bulletin/data/Surveillance_Bulletin_01_2017-01-12.csv>`_, you'll notice that the data looks pretty similiar to last mission, except that there is no latitude or longitude to geocode each row; however, each row does have a location field. We should be able to indentify those locations to actual areas on a map.

Search for "political boundaries geojson" and find `gadm.org <http://www.gadm.org/>`_. This site provides geospatial data about administrative boundaries for each state. Go to the `GADM Downloads Page <http://www.gadm.org/country>`_ to check out the data.

.. image:: /_static/images/GADM_download_page.png

Download the `shapefile for Brazil <http://biogeo.ucdavis.edu/data/gadm2.8/shp/BRA_adm_shp.zip>`_.

The file ``BRA_adm_shp.zip`` will download. Double click the file to unzip the file. You should see three shapefiles: ``BRA_adm0.shp``, ``BRA_adm1.shp``, ``BRA_adm2.shp``. ``BRA_adm3.shp``. Let's take a look at these shapefiles. In order to look at a shapefile, you will need download `QGIS <https://qgis.org/en/site/>`_, an open source desktop viewer for geospatial data. QGIS can be downloaded via the `Boundless site <https://connect.boundlessgeo.com/Downloads>`_. After downloading, double click the ``.dmg`` file to install.

.. note::

  Note: Use your personal email to register on Boundless Connect to get access to the QGIS download.

After QGIS is installed, drag the ``BRA_adm1.shp`` file into the QGIS window in order to import the file.

.. note::

  The zoom on the QGIS window is VERY sensitive. You may need to automatically zoom to the layer you would like to view. Right click on your layer in the *Layers Panel*, and select *Zoom to Layer*.

.. image:: /_static/images/QGIS_zoom_to_layer.png

Great! That looks exactly like what we need. Let's convert the file into GeoJSON so that we can serve it up from within our web application. We can use the ``ogr2ogr`` command. ::

  $ ogr2ogr -f "GeoJSON" brazil.geojson BRA_adm_shp/BRA_adm1.shp

After the command completes, check out the ``brazil.geojson`` file. Yikes! The file seems pretty big. Let's see how big: ::

  $ ls -lh brazil.geojson

.. image:: /_static/images/CLI_check_file_size.png

A 25M file is not going to work well in our web app. And that's just Brazil!

Fortunately, shapefiles can be compressed in size by reducing the amount of detail. In QGIS, select *Vector > Geometry Tools > Simplify geometries* from the top menu. Select your Brazil Geometry ``BRA_adm1`` and set the tolerance to ``0.05``. Hit Run.

.. image:: /_static/images/QGIS_simplify_geometries.png

QGIS should generate a new layer that looks pretty much the same as the last layer.

Right click on the newly created layer and select *Save As...*. Save the file as GeoJSON with the name ``brazil_compressed.geojson``. Be sure to type in the entire path of the file that you are creating.

.. image:: /_static/images/QGIS_save_as.png

Now if you check the size of the newly created ``brazil_compressed.geojson``, you should see that it is much smaller!

Run the command: ::

  $ ls -lh brazil_compressed.geojson


.. image:: /_static/images/CLI_check_compressed_file_size.png

.. note::

  A file size of 331K isn't great for a webapp; it's still a bit large. In a few weeks, we'll look at how some of the features of GeoServer allows you to display large amounts of data without a big download.

The last step is to join all of the GeoJSON files together. To do that, we can use a nice Node.js library from MapBox. Run the following commands: ::

  $ npm install -g @mapbox/geojson-merge
  $ geojson-merge argentina_compressed.geojson brazil_compressed.geojson columbia_compressed.geojson dominican_republic_compressed.geojson el_salvador_compressed.geojson equador_compressed.geojson guatamala_compressed.geojson haiti_compressed.geojson mexico_compressed.geojson
  nicaragua_compressed.geojson panama_compressed.geojson > states.geojson

To save you time, we went ahead and optimized the geometries for each country. Some might still need some work, but can
tackle that some day when you are bored.
