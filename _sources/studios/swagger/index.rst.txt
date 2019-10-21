:orphan:

.. _swagger-studio:

=============================
Studio: Springfox and Swagger
=============================

In this studio, we will be learning how to use Springfox to autogenerate the Swagger docs.

Getting Started
===============

Create a ``rest-springfox-solution`` branch off of your ``launchcart/rest-studio-solution`` branch.

.. warning:: Do *not* create the branch off of one of the walkthrough branches (``rest-swagger-starter`` or ``rest-swagger-solution``), since we manually created Swagger docs in that branch.

.. warning::

  We will be using some annotations and methods to configure Swagger documentation (via Springfox) for our API. There are Java-based configuration tools provided by Swagger that *are not* supported by Springfox, so not everything you find on the web will work in this context.

  Refer to the `Springfox documentation <http://springfox.github.io/springfox/docs/current/>`_ as your primary reference.

Configure Springfox
===================

Add these to the ``dependencies`` block in ``build.gradle``: ::

    compile(group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2')
    compile(group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.9.2')


Add ``@EnableSwagger2`` annotation to ``LaunchcartApplication.java``

.. code-block:: java

  @EnableSwagger2
  @SpringBootApplication
  public class LaunchcartApplication {

    public static void main(String[] args) {
      SpringApplication.run(LaunchcartApplication.class, args);
    }
  }

Within the same class, add a ``Docket`` bean to allow for some code-based configuration of Springfox:

.. code-block:: java

  @Bean
  public Docket apiDocket() {
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .select()
        .apis(RequestHandlerSelectors.any())
        .paths(PathSelectors.any())
        .build();
  }

  private ApiInfo apiInfo() {
    return new ApiInfoBuilder()
        .title("LaunchCart REST API")
        .build();
  }

The ``apiInfo`` method customizes the title of our API documentation, with the same effect as adding a ``title`` property to the ``info`` object in a Swagger YAML config file. You'll add some additional customizations below.

View the Generated Swagger Documentation
========================================

Now start your application. Navigate to http://localhost:8080/login to log in, and then go to http://localhost:8080/swagger-ui.html to view the generated Swagger docs.

.. Warning::

  Because of authentication code included in the LaunchCart application, this URL will not load unless you are logged in.

Tasks
=====

Bypassing Login
---------------

We provided a class for you called `AuthenticationInterceptor <https://gitlab.com/LaunchCodeTraining/launchcart/blob/rest-springfox-starter/src/main/java/org/launchcode/launchcart/AuthenticationInterceptor.java>`_ that handles what routes are allowed to users and non users alike. Take a look at the file to see what it is doing for you.

Figure out how to change ``AuthenticationInterceptor.preHandle`` to allow the Swagger docs to load when you are NOT logged in.

.. hint::

  Log out of LaunchCart, and then try to access `your Swagger docs page <http://localhost:8080/swagger-ui.html>`_ with your browser's developer tools opened to the Network tab.

  Note which resource requests return 3xx errors (that is, are redirected to the login screen). You'll need to modify ``AuthenticationInterceptor.preHandle`` to allow these requests through.

Display Only API Endpoints
--------------------------

Swagger is currently showing *all* endpoints, including non-API endpoints that return HTML. Figure out how to display *only* ``/api`` endpoints in the Swagger report.

.. hint:: You should refer to `section 6.1 of Baeldung's Springfox tutorial <https://www.baeldung.com/swagger-2-documentation-for-spring-rest-api#advanced>`_. And note that while we create a configuration bean above, we haven't put it to use yet!

Overriding Default Info
-----------------------

While the info provided by default in the auto-generated docs is fine, it could definitely be better. For example:

1. There isn't a lot of info about our API.
2. API methods are grouped and named by controller name rather than resource.
3. The return types of some methods doesn't reflect the actual return type. For example, in the case of ``PUT /api/items`` the stated return type is ``ResponseEntity``.

.. image:: /_static/images/springfox-docs-default-1.png

.. image:: /_static/images/springfox-docs-default-2.png

Let's address each of these.

Grouping API Methods by Resource
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When using Springfox with Spring Boot, Springfox is able to determine a lot about your API based on the Spring Boot annotations that you use. There are also `additional annotations <https://github.com/swagger-api/swagger-core/wiki/Annotations-1.5.X>`_ provided by Swagger that can be used to further enrich your API documentation.

In particular, the ``@Api`` annotation can be applied to a class to add tags and other settings. Read an `overview of the @Api annotation <https://github.com/swagger-api/swagger-core/wiki/Annotations-1.5.X#api>`_ to learn how to add tags to each of the methods within a class.

Adding API Info
^^^^^^^^^^^^^^^

We have already added one custom piece of info to our docs: the title. We can use additional methods of the ``ApiInfoBuilder`` class to add other ``info`` object properties such as ``contact``, ``license``, ``version``, and so on.

By referring to the following resources, add at least 3 additional informational fields to your documentation:

- `ApiInfoBuilder JavaDoc <http://springfox.github.io/springfox/javadoc/current/springfox/documentation/builders/ApiInfoBuilder.html>`_
- `ApiInfoBuilder example <https://github.com/springfox/springfox-demos/blob/master/boot-swagger/src/main/java/springfoxdemo/boot/swagger/Application.java>`_ (see line 139)

Documenting Correct Return Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Springfox scans our classes, reading annotations and method signatures (i.e. the number and type of paramters and return values) to determine the structure of our API. It assumes--reasonably, in many cases--that the return type of a method is the same as the return type of the API endpoint. This is not always the case, however.

The `@ApiOperation annotation <https://github.com/swagger-api/swagger-core/wiki/Annotations-1.5.X#apioperation>`_ allows you to specify the return type of an API method, among other things. Apply this annotation to each method with a ``ResponseEntity`` return type to properly specify the return type of the method.

.. hint::

  The linked example demonstrates several parameters of the ``@ApiOperation`` annotation, but you will only need two. The rest are optional and/or don't apply to our situation.

Turn in your Work
=================

* Commit and push your work to GitLab
* Create a merge request and post it to slack
* Notify the instructor that you are done, along with the name of the branch that you completed your work in
