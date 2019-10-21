:orphan:

.. _walkthrough-swagger:

======================
Walkthrough: SwaggerUI
======================

In this walkthrough, the instructor will guide through adding API documentation using `SwaggerUI <https://swagger.io/swagger-ui/>`_.

Concept
=======

REST allows us to programmatically utilize (Create, Read, Update, Delete) resources in a datastore. Anyone can make HTTP requests to our RESTful API to interact with our datastore, however how do we let them know which endpoints to access, and which HTTP verbs to use, and when to include query parameters, path variables, or JSON?

Swagger to the rescue!

Swagger is a documentation creation tool. Swagger is going to assist us in creating the documentation for our RESTful API. It will give users a webpage that contains all of the endpoints, which HTTP verbs they accept, if they take any additional information, and finally what happens when that HTTP request is made.

Getting Started
===============

The same Launchcart project and repo you used for the REST studio.
Create and checkout a branch named ``my-rest-swagger-starter`` with this command ``git checkout -b my-rest-swagger-solution``

Tasks
=====

#. Add SwaggerUI to our project

   - Create ``swagger.yaml``
   - Edit ``swagger/index.html`` to point to our ``swagger.yaml`` file

#. Edit Swagger YAML

   - Outline
   - Tags
   - Paths
   - Definitions
   - Parameters

Add SwaggerUI to the Project
----------------------------

Clone the `SwaggerUI repository <https://github.com/swagger-api/swagger-ui/tree/2.x>`_ from Github. Alternatively, download the code as a zip file and unzip it.

.. note:: We are downloading the SwaggerUI 2.x branch.

To add the SwaggerUI files to your project:

1. In IntelliJ, create the directory ``launchcart/src/main/resources/static/swagger``
2. Via terminal or file explorer navigate into the swagger-ui repo that you just cloned.
3. Copy the *contents* of ``swagger-ui/dist`` directory into ``launchcart/src/main/resources/static/swagger/`` directory. The ``dist/`` directory contains all of the HTML, CSS, and JavaScript required to generate a Swagger document

Create Swagger ``.yaml`` File
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the folder ``launchcart/src/main/resources/static/swagger``, create a ``swagger.yaml`` file. You can do this via IntelliJ, or by running ``touch swagger.yaml`` from the directory.

Edit swagger/index.html
^^^^^^^^^^^^^^^^^^^^^^^

Open ``swagger/index.html`` and locate the script block, the top of which should look like this:

.. code::

  <script type="text/javascript">
    $(function () {
      var url = window.location.search.match(/url=([^&]+)/);
      if (url && url.length > 1) {
        url = decodeURIComponent(url[1]);
      } else {
        url = "http://petstore.swagger.io/v2/swagger.json";
      }

      hljs.configure({
        highlightSizeThreshold: 5000
      });

Edit the ``var url = ...`` line so that it points to our ``swagger.yaml`` file, and delete the conditional immediately following. The top of the script block should look lik this:

.. code::

  <script type="text/javascript">
    $(function () {
      url = "http://localhost:8080/swagger/swagger.yaml";

      hljs.configure({
        highlightSizeThreshold: 5000
      });

Edit Swagger YAML
=================

Outline
-------

Next we need to begin writing the Swagger YAML file. Copy the following code into your ``swagger.yaml``.

.. code-block:: yaml

  swagger: '2.0'
  info:
    description: This is an example RESTful API
    version: 1.0.0
    title: LaunchCart API
    termsOfService: http://swagger.io/terms/
    contact:
      email: your.email@gmail.com
    license:
      name: Apache 2.0
      url: http://www.apache.org/licenses/LICENSE-2.0.html
  tags:
  paths:
  definitions:

Start up SpringBoot and navigate to the url http://localhost:8080/swagger/index.html. You should see a SwaggerUI page displayed. It will look something like this:

.. image:: /_static/images/swagger-ui.png

.. warning:: If your screen reports and error "failed to parse JSON/YAML response", then check the format of ``swagger.yaml`` to make sure it is correct.

Now we can start adding info about our API endpoints. Let's start with the ``/api/carts`` path.

Tags
----

Add two entries to the ``tags`` section, one for each collection of resource endpoints that we'll be working with (carts and items).

.. code:: yaml

   tags:
     - name: cart
      description: Cart provides access to all of the items you are about to buy.
    - name: item
      description: Items to be added to cart.

Refresh your browser to see the results.

.. warning:: YAML is white-spaced based. Be *very* careful with tabs and spaces. You may also find the `YAML Reference <http://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html>`_ helpful.

Paths
-----

Also, let's add the ``GET`` endpoint for ``/api/carts`` in the ``paths`` section.

.. code-block:: yaml

  paths:
    /api/carts:
      get:
        tags:
        - cart
        summary: Returns all carts that exist.
        operationId: getAllCarts
        produces:
        - application/json
        responses:
          200:
            description: successful operation


Now, let's fill in the schema for the ``/api/carts`` endpoint. In order to do that, let's get some example output from our API.

Visit ``http://localhost:8080/api/carts`` or load the endpoint in the RESTED plugin. You should receive something that looks like this:

.. code-block:: json

  [
    {
      "uid": 1,
      "items": [
        {
          "uid": 1,
          "name": "Chacos",
          "price": 100,
          "newItem": true,
          "description": "I think they're sandals"
        }
      ]
    }
  ]


Using this info, update the ``/api/carts`` definition to this (not the new ``schema`` section):

.. code-block:: yaml

  paths:
      /api/carts:
          get:
              tags:
              - cart
              summary: Returns all carts that exist..
              operationId: getAllCarts
              produces:
              - application/json
              responses:
                  200:
                    description: successful operation
                    schema:
                      type: object
                      required:
                      - uid
                      - items
                      properties:
                        uid:
                          type: integer
                          format: int32
                          example: 34
                        items:
                          type: array
                          items:
                            $ref: "#/definitions/Item"

Let's also add a path for our Items resources.

.. code:: yaml

    /api/items:
      get:
        tags:
        - item
        summary: Returns items
        operationId: getItems
        produces:
        - application/json
        responses:
          200:
            description: successful operation
            schema:
              type: array
              items:
                $ref: "#/definitions/Item"

Refresh your browser to see the updated info.

.. note::

   Make sure that your whitespace is correct. There can only be a one tab indent for every map.

   Incorrect indentation may cause your API endpoints not to show up or display errors.

Definitions
-----------

We can define types that are returned, to provide examples of sample responses, along with data type info. Add the below ``yaml`` to the ``defintions`` section. Notice that this is referenced in the ``responses`` section of ``/api/cart``.

.. code:: yaml

  definitions:
    Item:
      type: object
      properties:
        uid:
          type: integer
          format: int32
        name:
          type: string
          example: "Chacos"
        price:
          type: number
          format: int64
          example: 100
        newItem:
          type: boolean
          example: true
        description:
          type: string
          example: "I think they're a type of sandals"

Parameters
----------

But wait, ``/api/items`` has two optional query parameters ``/api/items?price=99&new=true``. Add the following ``parameters`` section within the ``/api/items`` path definition:

.. code:: yaml

  parameters:
    - in: query
      name: price
      schema:
        type: double
      required: false
      description: match items by price
    - in: query
      name: new
      schema:
        type: boolean
      required: false
      description: match items by newItem true/false

Again, reload your browser to see the new info displayed in SwaggerUI.

.. note:: There are two types of parameters: ``query`` and ``path``.  See the `Swagger documentation <https://swagger.io/docs/specification/describing-parameters/>`_ for more info about documenting parameters.

Let's look at an example that uses path parameters.

.. code-block:: yaml

  /api/items/{id}:
  get:
    tags:
    - item
    summary: Returns an individual
    operationId: getItem
    produces:
    - application/json
    responses:
      200:
        description: successful operation
        schema:
          $ref: "#/definitions/Item"
    parameters:
    - in: path
      name: id
      schema:
        type: integer
      required: true
      description: The ID of an item in the system

You can keep going like this to fully document your API. Now that we know how Swagger works, however, we can use a simpler method to automatically create API documentation using Swagger.
