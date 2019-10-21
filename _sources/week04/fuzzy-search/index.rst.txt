:orphan:

.. _week4-fuzzy-search:

================================
Fuzzy Search for Report Keywords
================================

We have completed a large part of our stakeholder requirements already, but have so far neglected one key request: Elasticsearch.

So far all the requests pull from JpaRepositories, and only touch PostGIS. We have not needed to use Elasticsearch yet so we have conviently worked around it. However, now that we have an explict request for fuzzy search it's time to work this technology into our project.

So what are we going to have to do?

Tasks:
    - Configure Elasticsearch
    - Configure Spring to work with Elasticsearch
    - Seed Elasticsearch from our PostGIS data
    - Amend our Controllers
    - Add empty search box, and button to HTML
    - Amend ``script.js``

Configure Elasticsearch
-----------------------

Before we use Elasticsearch in our project we first need to setup Elasticsearch.

Like with PostGIS it would be a good idea to create a new docker container specifically for our container. Giving us additional control over how Elasticsearch runs on our machine.

Review :ref:`docker-elasticsearch` for help with creating your new Elasticsearch container.

.. note::

   Remember to stop any running elastcisearch containers you may have running on your machine. It would also be a good idea to name your elasticsearch container for this week something unique like: ``es-zika-week4``.

Configure Spring to work with Elasticsearch
-------------------------------------------

Now we will need to configure our Spring application to work with Elasticsearch.

Review :ref:`spring-elasticsearch` for assistance.

Seed Elasticsearch from our PostGIS Data
----------------------------------------

Now that we have Elasticsearch and Spring working together, we need to get all of the report data out of our PostGIS database, and into our Elasticsearch cluster.

In :ref:`walkthrough-elasticsearch-spring` we create an ``EsController.java`` file which was a RestController with an endpoint that would refresh() our connected Elasticsearch cluster.

We also added code to the ``EsUtil.java`` file that pulled everything out of our ``itemRepository`` and then saved it all to our ``itemDocumentRepository``.

We want to do a similar thing for our project. But instead of an ``itemRepository`` and ``itemDocumentRepository`` we want to use ``reportRepository`` and ``reportDocumentRepository``.

After creating this code you will have to make a POST request to the endpoint you defined to refresh your Elasticsearch cluster.

.. note::

   This will probably take a few minutes. It is loading 240,000 records from our database, and then indexing them into our Elasticsearch cluster. Don't panic if it looks like it isn't doing anything for a few minutes.

Finally to check that Elasticsearch has been seeded correctly you can make a GET request to the new report index: ``$ curl -XGET 127.0.0.1:9200/report/_count``. This request should give you over 240000 documents.

Amend our Controllers
---------------------

Elasticsearch is running, connected to our web app, and has been filled with all of the report data. However, we still aren't using Elasticsearch in our project. We need to change how our Controllers feed data to our ``script.js`` file.

Currently our ``script.js`` file makes an AJAX fetch() request including a query parameter with the date. This endpoint only goes to PostGIS to perform the search.

We will need to create a new Controller specifically for Elasticsearch requests. We recommend creating a file called ``ReportDocumentController.java``. In this file you will need to create a new endpoint that behaves similarly to the endpoint in your ``ReportRestController.java``. However, instead of going to PostGIS, it will go to Elasticsearch to retreive data.

In this new controller file you will need to perform a fuzzy search, you will have to join the documents with location data from PostGIS, and you will have to return it together as a FeatureCollection.

Add Search Box and Button to ``index.html``
-------------------------------------------

Our users will need a way to make a search. They will expect a search box, and search button.

You will need to create these in your ``index.html`` file, giving them specific ids so you can reference them in your ``script.js`` file.

You may find the `jQuery click() documentation <https://api.jquery.com/click/>`_ useful, you may also want to reference the `MDN textbox documentation <https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/text>`_.

Amend ``script.js``
-------------------

Finally after creating, and amending our previous controllers, and creating the HTML elements we need. We can update the calls made by our ``script.js`` file to call the new endpoint.

You will have to add some logic to your ``script.js`` file to make a request based on if an input term is provided in the text box.