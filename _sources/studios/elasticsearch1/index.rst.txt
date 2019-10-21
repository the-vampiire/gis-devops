:orphan:

.. _elasticsearch1-studio:

==========================
Elasticsearch Query Studio
==========================

Setup
=====

We will need to verify:
    - Elasticsearch is installed
    - Kibana is installed
    - Elasticsearch is seeded with data

Elasticsearch Installation
--------------------------

We will be running Elasticsearch as a docker container. You can check if you have docker installed with: ``$ docker -v``, if it's installed it will print out the version installed. Check out the :ref:`docker` if you need to install docker.

After docker is installed you can check your containers with: ``$ docker ps -a``, if you don't have an Elasticsearch container, you can get one by following the :ref:`docker-elasticsearch`.

Kibana Installation
-------------------

We will be running Kibana as a docker container. If you don't already have a Kibana container, you can get one by following the :ref:`docker-kibana`.

Seed Elasticsearch
------------------

Elasticsearch doesn't have any data in it first, so we will first need to run a script to seed the data for our studio.

Seed ``/tweets``:
    #. Copy `tweets.sh <https://gitlab.com/LaunchCodeTraining/elasticsearch-practice/blob/master/tweets.sh>`_ to your local machine as baseball.sh.
    #. Make the script excutable from the terminal: ``$ chmod 500 tweets.sh``
    #. Run the script: ``$ ./tweets.sh``

Kibana Introduction
-------------------

In the :ref:`walkthrough-elasticsearch` we worked with Elasticsearch using cURL. An easier way to work with Elasticsearch is by using Kibana.

To access Kibana go to ``localhost:5601`` in a web browser. After clicking through the splash screen, click Dev Tools from the Kibana menu.

.. image:: /_static/images/kibana/dev-tools.png 

This will take you to an area where you can make HTTP Requests, and view the Response in a more streamlined, and interactive environment. This will be a much nicer area to work than using cURL from the command line. However, when we start working with Elasticsearch in AWS you will need to continue working with cURL, so don't completely forget about cURL.

Let's take a look at ``GET /teams/_search``.

.. image:: /_static/images/kibana/teams-search.png

We can make the same HTTP Requests we would make from the terminal with cURL in Kibana. The syntax changes slightly since Kibana already knows the URL, and port for your Elasticsearch container. It also adds the JSON for us, and sets the HTTP Header to allow JSON. This simplifies our lives greatly. Try out Kibana during this studio.

Your Tasks
==========

Now you'll get some practice with Elasticsearch. Carry out each of the following tasks. Once you have a successful query/command for each, save the commend in a ``.txt`` file for submission

1. Get information about the ``twitter`` index
2. Fetch the first 10 documents of the index
3. Fetch the last 10 documents of the index
4. Add a document to the index
5. Fetch the document that you just added
6. Delete a the document that you just added from the index
7. Get all tweets by John Smith
8. Get the average number of likes for all tweets

The :ref:`walkthrough-elasticsearch` prepared you for most of these tasks, however you may need to reference the `Elasticsearch documentation <https://www.elastic.co/guide/en/elasticsearch/reference/6.5/index.html>`_, and work with your fellow classmates to answer all the questions!

Turning In Your Work
====================

Submit your queries to an instructor via Slack once you're finished.
