:orphan:

.. _week2_display-map:

=========================================
Project Week2: Display an Interactive Map
=========================================

Now that our project is setup our first objective is to display the base map from OSM.

Tasks:
    - Create templates/index.html
    - Create static/js/script.js
    - Create controllers/IndexController.java
    - Create tests/IndexControllerTests.java
    - Commit!

Completing these tasks should create an endpoint handler (IndexController.java) that serves a Thymeleaf template (index.html) that has some placeholder code that Javascript (script.js) puts a map from OSM into.

Also we are creating a Controller, so we will need to create a test file to ensure that the controller behaves correctly.

Create templates/index.html
---------------------------

Spring is configured to serve a Thymeleaf template from every endpoint handler inside of any Controller that includes the ``@Controller`` annotation. We will need to create this ``templates/index.html`` file, add any JavaScript, or OpenLayers dependencies, and create some HTML tags that will hold our map.

It's tough to remember how we loaded all the dependencies into our index.html file, so look at how we did this in the `OpenLayers and jQuery walkthrough <../../walkthroughs/openlayers/>`_ to help yourself out.

If you are struggling with adding empty HTML tags look over your solution to the `Airwaze studio <../../studios/airwaze/>`_. It may be benefical to look at the solution you created for this studio instead of the Studio instructions.

Create static/js/script.js
--------------------------

We will be using Javascript to load the map from OSM, and to create new layers to add to that map. So we will need to create a ``resources/js/script.js`` that includes this information.

Again looking at the `OpenLayers and jQuery walkthrough <../../walkthroughs/openlayers/>`_ or your solution to the `Airwaze studio <../../studios/airwaze/>`_ would be benefical in completing this task.

.. hint::
   
   You will need to make sure the ``index.html`` file you created earlier links to this new ``script.js`` file.

Create controllers/IndexController.java
---------------------------------------

Now that we have a thymeleaf template, and a Javascript file that loads an OSM map, we will need to configure the backend of our application to listen for an HTTP request, and to serve up our new Thymeleaf template as an HTTP Response. We do this by creating a new Controller in Java, and using the ``@Controller`` Spring annotation.

.. note::
   
   Since our Spring project came with Apache Tomcat as the web server which is configured to automatically serve an index.html file found in the resources folder, it is possible to forgo creating a Controller for the root of our project. However, this is not a best practice because we would be unable to test if Apache Tomcat is serving the file correctly!

We have not covered Spring Controllers in this class, as it was addressed in the prep-work. However, creating a Spring Controller is necessary to this project, so we will show you how to setup your IndexController.

.. sourcecode:: java
   :caption: IndexController.java

   // import statements not listed

   @Controller
   @RequestMapping(value = "/")
   public class IndexController {

       @GetMapping
       public String getIndex() {
           return "index";
       }
   }

This controller is for the root of our project ``localhost:8080/`` or ``localhost:8080``. The method ``getIndex()`` is for HTTP GET requests made to the root. The only thing this method does is return a String which is a representation of location, and name of the Thymeleaf template to be returned as an HTTP Response.

In this case ``return "index";`` refers to a file named index.html in the templates folder of our Spring application.

Create tests/IndexControllerTests.java
--------------------------------------

Since we have created a new Controller file we need to test each method inside the file. In this case we need to test the ``getIndex()`` method.

To properly create this integration test we will need to create three new files: ``IntegrationTestConfig.java``, ``IndexControllerTests.java`` and ``application-test.properties``.

Look over the `Integration Testing walkthrough <../../walkthroughs/spring-integration-testing/>`_ and your solution to the `LaunchCart part 1 studio <../../studios/launchcart1/>`_ for assistance in completing this task.

You will want to make sure when a user makes an HTTP GET request to: ``localhost:8080/`` they get an HTTP Response of 200.

You will want to look into the ``@TestPropertySource(locations = "classpath:application-test.properties")`` annotation. It let's you use a different application.properties file. You will need to add this to add this to your ``IntegrationTestConfig.java`` file, and any other test files that need to work with the database. 

You will need to create and configure the ``application-test.properties`` file. You should model it after your current application.properties file, but configure your environment variables to point to the testing database you completed in the previous primary objective.

After you have created the three files, create a new gradle task for ``test``, and ensure your tests pass. Don't forget to use environment variables in your ``application-test.properties`` file!

Verify the Map is displayed
---------------------------

Our test verifies the user receives an HTTP response of 200, but it doesn't check that our map loaded correctly. You will need to manually check that.

Run bootRun go to ``localhost:8080/`` in your browser and manually check that the Map is displayed in the browser.

If you don't see your map, check out the developer tools in your browser to determine what resources failed to load.

Commit!
-------

Finally, commit your work after completing this primary objective!

`Back to week2! <../project/>`_