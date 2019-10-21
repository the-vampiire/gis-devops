.. _week4_project:

===================================================
Week 4 - Project Week: Zika Mission Control Part 2
===================================================

Project Requirements
====================

Following are the requirements from our stakeholders, and our tech team.

Stakeholder Requirements
------------------------

- Users can add report data through the web app.
- Users can search for keywords in all reports. Searches that include typos still yield results.
- Users can view reports over time.

Technical Requirements
----------------------

- Data changes: The underlying CSVs that drive our web app have changed. The database will need to be altered to accommodate this different data.
- Rest: Users READ, and ADD report data via a Rest Controller.
- Rest Documentation: Swagger/Springfox used to create Rest API Endpoint documentation.
- Datastore: Elasticsearch needs to be used as the secondary data store for report data. Elasticsearch fuzzy search should be implemented to allow for flexible keyword searching.

Primary Objectives
==================

You should **complete all primary objectives** before working on any secondary objectives.

For your primary objectives articles have been provided to help you think about what needs to be completed to pass the objective.

0. :ref:`week4-new-data`
1. :ref:`week4-report-via-hard-coded-date`
2. :ref:`week4-report-via-user-selected-date`
3. :ref:`week4-fuzzy-search`

Secondary Objectives
====================

We have already done a fair amount this week, and we want everyone to at least complete the primary objectives. But there is still so much we can do with this project. We can utilize the speed of Elasticsearch, we can further expand on ReST principles, we can change the look and feel of our state layer. We have outlined a few secondary objectives for you, but if you think of some other way to implement these changes, go for it!

- Refactor so all report data is served from elasticsearch
- Refactor so ReSTful Endpoints are used any time data is touched
- Allow users to POST new report data -- save it both to the PostGIS and Elasticsearch
- Write some of your own elasticsearch queries with the @Query annotation
- Create Swagger documentation for your ReSTful API endpoints
- Increase test coverage by adding tests for all Controllers, and DataRepositories
- Learn how the multi-polygons were created: :ref:`week4-adding-boundary-geometries`

Turning in Your Work
====================

Code Review
-----------

Let your instructor know When you complete the primary objectives. The instructor will need a link to your GitLab repo, and they will peform a code review, and leave you feedback.

Objective Checklist
-------------------

As you work through the objectives for this week, keep track of them on your Checklist, your instructor will also confirm which objectives you completed in their code review. If you don't pass an objective the instructor will give you feedback on what you need to do to complete that objective.

Presentation
------------

Friday afternoon, everyone will present their project to the class. This presentation is meant to be a celebration of your hard work throughout the week, and as a chance for you to share, and learn from the other students in the class.

At the end of this course, during your graduation ceremony you will be expected to present your final project to the attendees. Every project week we will have a presentation as a way for you to practice for this final presentation.

Check Your Knowledge
====================

To reinforce your understanding of the concepts answer these questions to yourself:
    - How is ``@RestController`` different from ``@Controller``?
    - What query parameters can be made to the different endpoints of your application?
    - What happens when a request doesn't include a query parameter?
    - How is a JpaRepository different from an ElasticsearchRepository?
    - What is the journey of our data?
    - How did we convert our CSV files into our PostGIS database?
    - How did we convert our PostGIS records into our Elasticsearch cluster?
    - How do we change our report layer?
    - Are there any other ways to change the report layer?
    - What's the difference between a GeoPoint and a MultiPolygon?
    - What is a FeatureCollection and what is it's relationship to GeoJSON?
    - What makes GeoJSON different from JSON?
