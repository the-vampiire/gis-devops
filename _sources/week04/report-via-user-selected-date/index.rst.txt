:orphan:

.. _week4-report-via-user-selected-date:

============================================
Display Report Data via a User Selected Date
============================================

Now that we have a map that is displaying a hard-coded date, let's give the user the ability change the date!

We have a few things to consider:
    - How does the user select a date?
    - Can users select from all dates, or only dates with report data?
    - If report data is missing location data, do we show that date?
    - Where on our webpage should the user select a date?

In a collaborative team environment you would probably discuss these questions. You would probably get input from the users of this application, or various other stakeholders. Since we emphasize learning, and exploration in this class, you will be able to answer these questions yourself.

For the purposes of this article, we will be assuming the user will be selecting the date from a pre-populated drop-down box of dates with report data. You may choose to go a different way, which is great, but your tasks will differ from the tasks in this article.

Tasks:
    - Add an empty drop-down box to ``index.html``
    - Populate drop-down box when ``script.js`` loads
    - Update map when drop-down box changes in ``script.js``

Add Empty Drop-down to ``index.html``
-------------------------------------

To give the user a list of options we will need an empty vessel to hold all of their options.

Checkout the `MDN Option docs <https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option>`_ for help on creating a drop-down box. Don't forget to give it a meaningful id as that's what we will use in our ``script.js`` file.

Populate Drop-down on Load
--------------------------

Now that we have an empty drop-down box we need to populate it with the dates of our report data.

Somewhere within our ``script.js`` file we will need to make an AJAX call to an endpoint that will return a list of *unique* report dates. We can accomplish this by creating a new endpoint in our report controller that calls our report repository.

You will have to amend your ``ReportRepository.java`` class to include a method that will return the results of a custom SQL query. This is not something we've done in this class, but you should try it out by reading about the @Query annotation in `this Baeldung article <https://www.baeldung.com/spring-data-jpa-query>`_.

To write your SQL statement you may find the `SELECT DISTINCT option <https://www.postgresql.org/docs/9.0/sql-select.html>`_ valuable.

After adding to ``ReportRepository.java`` you will have to amend a controller file to serve this new method, and give it an endpoint. You will then be able to make a request to the endpoint to get a collection of string dates.

When you have a collection of string dates in JavaScript you will have to add them to the drop-down box we added earlier.

Update Map when Drop-down Changes
---------------------------------

Now that we have a drop-down box populated with all of the distinct dates from our report data. We need change our map everytime the drop-down box changes.

You can create an event listener for your drop-down box. When it changes you can change the source of your report layer by making a new request to the endpoint you created in the previous objective to get report data.

Look over the `jQuery change documentation <https://api.jquery.com/change/>`_ to help guide you towards your solution.

You may also want to look over the `OpenLayers removeLayer <https://openlayers.org/en/latest/apidoc/module-ol_Map-Map.html#removeLayer>`_ and `OpenLayers addLayer <https://openlayers.org/en/latest/apidoc/module-ol_Map-Map.html#addLayer>`_ methods.remove

After you build in this functionality run your application and test a few dates using your new drop-down box. You may notice it takes some days longer to load than others, and some days may not include any data. These are things to look into, but shouldn't be addressed until after you finish all the primary objectives.