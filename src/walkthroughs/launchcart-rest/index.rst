:orphan:

.. _walkthrough-launchcart-rest:

============================
Walkthrough: LaunchCart REST
============================

In this walkthrough, the instructor will guide you through adding some RESTful endpoints to the LaunchCart application.

Concept: REST
=============

REST stands for:
    * Representational
    * State
    * Transfer

Our web application has access to data, and we want to make that data accessible to the user. We are going to pass a *representation* of that data to the user. The user can make as many changes to their representation of the data as they'd like. If they want to save those changes, they will send back a new representation of their data that reflects their changes. Our web application will then be able to update our data store appropriately based on the new representation sent by the user.

In this class we will use JSON as the representation of our data, but other ways of representing data include: XML, YAML, TOML, etc.

We will be using HTTP to send these representational states back and forth as it's the primary protocol used by web applications.

There are four HTTP methods we will use in our RESTful APIs:
    * GET
    * POST
    * PUT
    * DELETE

The HTTP method defines what the user is trying to do with their representational state.
    * A GET request is asking for an existing resource. The HTTP Response would include a JSON representation of the resource if it exists.

    * A POST request is sending a representation (JSON) of a resource that the user would like to save. The HTTP Response needs to acknowledge that the representation of the data was stored successfully.

    * A PUT request is sending a representation (JSON) of an existing resource that needs to be updated. The HTTP Response needs to acknolwedge that the changes have been saved, and the resource was updated succssfully.

    * A DELETE request is asking that an existing resource needs to be deleted. The HTTP Response needs to acknowledge that the resource was successfully deleted.

Takeaways:
    * GET, and a DELETE requests don't need to include a representation of the resource in the HTTP Request. The resource that needs to be retrieved, or deleted is indicated by the URL.

    * POST, and PUT requests require a representatoin of the resouce to be included in the HTTP Request. The HTTP Response may, or may not include a JSON representation of the data that was saved/updated.

Getting Started
===============

From the same ``launchcart`` `project/repository <https://gitlab.com/LaunchCodeTraining/launchcart>`_  that you used previously, check out the ``rest-walkthrough-starter`` branch. Then create a story branch.::

    $ git checkout rest-walkthrough-starter
    $ git checkout -b rest-walkthrough-starter-solution

If you haven't already, install the Rested browser plugin: `Firefox <https://addons.mozilla.org/en-
US/firefox/addon/rested/>`_ | `Chrome <https://chrome.google.com/webstore/detail/rested/eelcnbccacci
pfolokglfhhmapdchbfg>`_. We'll use this to manually query our REST API. If you are familiar with `cURL <https://curl.haxx.se/>`_ then you may also use that tool to query the API.

What's New
==========

This starter code has some functionality beyond what you added in  :ref:`launchcart-part2`. In particular, it has a ``Customer`` class, along with functionality for users to register and log in as customers.

.. hint::

    A good way to see what has been added in a branch is to you use the **comparison** feature of Gitlab.

    Example: View the `launchcart branches <https://gitlab.com/LaunchCodeTraining/launchcart/branches>`_ and notice the **Compare** button next to each branch.

    This can also be done in the terminal using `git`::

        $ git diff launchcart2 rest-walkthrough-starter



Adding a REST Controller
========================

Let's complete a few setup steps before starting to code:

* Create a new package, ``org.launchcode.launchcart.controllers.rest``
* Create a new class in this package named ``ItemRestController``
* Annotate the class with ``@RestController``

In the ``test`` module, note that there are two new classes:

- ``AbstractBaseRestIntegrationTest`` contains a couple of utility methods to handle serializing Java objects for the purposes of testing. 
- ``ItemRestControllerTests`` extends ``AbstractBaseRestIntegrationTest`` and contains the integration tests for the functionality that we'll be adding. We'll review this code in class.

Our Tasks
=========

We will now implement the Item resource in the following ways:

* ``GET /api/items`` (with parameter ``price``)
* ``GET /api/items/{id}``
* ``POST /api/items``
* ``PUT /api/items/{id}``
* ``DELETE /api/items/{id}``

Bonus Mission
=============

Enable XML as a resource format. To do this, add the following Gradle dependency::

    compile('com.fasterxml.jackson.dataformat:jackson-dataformat-xml')

Now annotate the ``Item`` class with ``@XmlRootElement``. Then add ``@XmlElement`` to each field that should be included in the XML serialization as an XML element child of ``<Item>``, and ``@XmlAttribute`` to each field that should be included as an XML attribute of ``<Item>``. Don't forget about inherited fields.

Spring Boot enables JSON formatting/serialization and makes it the default. If you wish XML to be the default format, you can set this up in ``WebApplicationConfig`` by adding:


.. code-block:: java

    @Override
    public void configureContentNegotiation(ContentNegotiationConfigurer configurer){
        configurer.defaultContentType(MediaType.APPLICATION_XML);
    }
