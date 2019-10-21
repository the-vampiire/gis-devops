:orphan:

.. _elasticsearch2-studio:

==============================
Elasticsearch Queries Studio 2
==============================

In this studio you will practice working with indices (deleting, reindexing) and mappings. You will also run some filtered and geo queries on Elasticsearch.

Loading Data
============

We will be working with the same data set as yesterday for ``/tweets``. However, we are adding location data for our tweets. Use the `tweets-geo.sh script <https://gitlab.com/LaunchCodeTraining/elasticsearch-practice/blob/master/tweets-geo.sh>`_ to create a new index: ``/tweets_geo``.

Look over the script as it may help you with your tasks.

Your Tasks
==========

Carry out each of the following tasks. Once you have a successful query for each, save the command in a ``.txt`` file for submission.

#. What is the data type of the ``location`` field in the ``twitter_geo`` index? What should it be?
#. Fix the issue with ``location`` by editing tweets_geo.sh to explictlly map ``location`` as a ``geo_point`` field. **Hint**: To build the JSON that you'll need to create the new mapping for ``twitter_geo``, you can copy the reponse from fetching the mapping for ``twitter`` and modify it.
#. Find all tweets with between 5 and 10 likes, inclusive of those endpoints.
#. Find all tweets by Mary Jones that have at least 2 likes.
#. Find all tweets that contain the text "Elasticsearch", including mispellings up to distance of 2 away.
#. Find all tweets that have a location (Hint: Try the exists query https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-exists-query.html)
#. Find all tweets that do not have a location
#. Find the average number of likes for tweets that have a location
#. Find the average number of likes for tweets that do not have a location
#. Find all tweets with locations within 500km of Boise, ID

Turning In Your Work
====================

Submit your queries to an instructor via Slack once you're finished.
