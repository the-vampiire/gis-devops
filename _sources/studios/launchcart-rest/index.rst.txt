:orphan:

.. _launchcart-rest-studio:

=======================
Studio: LaunchCart REST
=======================

In this studio, we'll add additional RESTful endpoints to the LaunchCart application. It builds upon the changes we added in the :ref:`walkthrough-launchcart-rest`.

Getting Started
===============

From the same ``launchcart`` `project/repository <https://gitlab.com/LaunchCodeTraining/launchcart>`_ that you used previously, make sure you are in the ``rest-walkthrough-starter`` branch that you used earlier today.

If you don't have the walkthrough code, you can fetch changes from the remote and check out the ``rest-studio-starter`` branch.

Create a story branch::

    (switch to the rest-studio branch)
    $ git checkout rest-studio-starter

    (-b will create a branch and then checkout that new branch)
    $ git checkout -b rest-studio-solution

Write integration tests before coding each controller method below. In doing so, you may find the documentation for `JsonPath <https://goessner.net/articles/JsonPath/>`_ to be useful. Also, refer to existing tests in ``ItemRestControllerTests``.

Tasks
=====

- Use TDD to write tests before writing code
- ``GET /api/items`` endpoint can take an additional optional parameter: new
- Add ``/api/customers`` Resources
- Add ``/api/carts`` Resources

Add ``new`` Parameter to ``/api/items``
=======================================

Add a ``new`` parameter to the ``GET /api/items`` endpoint. Note that this should work **in conjunction** with the ``price`` parameter, so that one, both, or neither can be used when querying the resource.

.. hint::

    You'll need to create one or more new query methods in `ItemRepository`. You might find the documentation on Spring Data JPA `query creation <https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.query-creation>`_ helpful for this.

Add ``/api/customers`` Resources
================================

Add the following REST resources:

* ``GET /api/customers``
* ``GET /api/customers/{id}``
* ``GET /api/customers/{id}/cart``

Write integration tests before coding each controller method. When testing, be sure to test for the expected status code, content type, and response contents.

Add ``/api/carts`` Resources
============================

Add the following REST resources:

* ``GET /api/carts``
* ``GET /api/carts/{id}``
* ``PUT /api/carts/{id}`` - allow items to be added to a cart

A cart resource should include information about its owner, as well the items contained in the cart. The ``PUT`` method should allow for items to be added and removed from the cart.

Bonus Mission
=============

If you finish the primary studio tasks, try to enable HATEOAS for your REST controllers by referencing the Spring tutorial, `Building a Hypermedia-Driven RESTful Web Service <https://spring.io/guides/gs/rest-hateoas/>`_. The sections titled "Create a resources representation class" and "Create a RestController" are most relevant.

Turning In Your Work
====================

If you don't complete each of the tasks, turn in as much as you have completed by the end of the day.

* Commit and push your work to GitLab
* Create a merge request for your `rest-studio-solution`
* Post the URL to your merge request in slack
