:orphan:

.. _launchcart-part2:

=========================
Studio: LaunchCart Part 2
=========================

In this studio, we revisit the LaunchCart application https://gitlab.com/LaunchCodeTraining/launchcart that we worked on in yesterday's studio :ref:`launchcart-part-1-studio`. We'll refactor the app to use Postgres, and add a couple of features.

Setup
-----

1. In the terminal go to project folder for **LaunchCart**
2. Checkout the branch that contains the solution for the Day 3 studio. Example: ``$ git checkout day3-solution``
3. Create a new branch for today's work: ``$ get checkout -b day4-add-hibernate``

.. tip::

    If you have any uncommitted changes, stash them using ``git stash``. You can retrieve them later via ``git stash pop``.

Your Tasks
----------

Each section outlines one task or group of tasks you need to complete.

Refactor to Use Hibernate (Keep Existing Functionality)
=======================================================

1. `Set Up A Postgres DB <../../installations/docker-psql/>`_
2. `Configure the App To Use the Database <../../walkthroughs/spring-data-jpa-hibernate/>`_ (controllers, entities, repositories, tests)
3. Get the tests to pass
    * Did you add JPA, and PSQL dependencies to ``build.gradle``?
    * Did you add an ``application.properties``, and ``application-test.properties``?
    * Did you create the appropriate databases, and users in Postgres?
    * Did you create IntelliJ environment variables for your runtime configurations?
    * Did you update your Models?
    * Did you set ``@Id`` and ``@GeneratedValue``?
    * Did you change your memory repositories to ``JpaRepositories``?
    * Did you autowire your new ``JpaRepositories`` into your controller files, and test files?
    * Did you update ``IntegrationTestConfig.java``?
    * Did you update ``LaunchCartApplicationTests.java``?
4. Commit your changes (For help see :ref:`git-reference`)

.. hint::

    You may have some tests that are unnecessary with our new changes. Are there tests, or testfiles we can remove?

Add newItem to Item Model
=========================

Let's add a new boolean property to ``Item`` to keep track of whether or not an item is new. Following TDD let's write our test first.

- Create a ``TestItem.java`` file if it doesn't already exist.
- Add a test to ``TestItem.java`` that creates a new Item, sets its newItem field to true, and then assertTrue that property.
- Modify the ``testNewItemFormCreatesItemAndRedirects`` test within ``ItemControllerTests`` to post an additional parameter, ``newItem``, with value ``"true"``.

Our new test fails because we haven't made the changes to the Item class to reflect our new tests. Let's fix that.

- Add a boolean field ``newItem`` to ``Item``, along with a getter and setter. Add ``@NotNull`` to the field.
- Run all of the tests to ensure that they pass

Let's do a little manual (eye) testing as well.

- Add a checkbox to ``templates/item/new.html``::

    <div class="form-group">
        <input type="checkbox" id="newItem"
                name="newItem" value="true" checked="checked" />
        <label for="newItem">New Item</label>
    </div>

- Run the app (bootRun, make sure you have environment variables) and ensure the New Item form submits, and that the chosen value is properly set in the database.
- Commit your changes (For help see :ref:`git-reference`)



Bonus Mission
-------------

Another basic feature that is missing is the ability to add some quantity of items (greater than 1) to the cart. To do this, we need to introduce a new model class, ``CartItem``.

.. Tip::

    Before embarking on this mission, create a story branch to isolate your work. Aside from being a best-practice, this will also keep your ``master`` branch nice and clean in the event you don't finish the mission. This will be helpful since we'll continue working on this app in a future studio.

Create a ``CartItem`` class with fields ``item`` and ``quantity``, and refactor ``Cart`` to store a collection of ``CartItem`` objects instead of ``Item`` objects. As ``Cart`` and ``Item`` have done, you should extend ``AbstractEntity`` to get the common identifier configuration contained in that base class.

Start by refactoring the model:
* Add the new model class
* Re/write tests in ``TestCart`` as necessary
* Refactor `Cart` to use ``CartItem``

Once you have a working model that uses ``CartItem``, run **all** of your tests, including the integration tests. You'll have some work to do here, since changing the model will break parts of the controller and view.

.. hint::

    You may experience a situation where your integration tests fail because a new item that is seemingly added to the cart isn't actually there when viewing ``/cart``. If this is the case, it's likely that Hibernate isn't persisting your new ``CartItem`` instances. These objects are never handled directly by the controller, and thus never saved via a repository.

    You can force Hibernate to save new ``CartItem`` objects in all situations by adding a cascade property to the ``@OneToMany`` collection storing ``CartItem`` objects via ``@OneToMany(cascade = { CascadeType.ALL })``


After your model has been refactored, and all of your tests pass, you'll need to refactor the controller and view layers heavily to get this to work. This will include adding functionality that allows the user to enter a quantity when adding an item to the cart.

As always, write your tests first!

Turning In Your Work
--------------------

If you don't complete each of the tasks, turn in as much as you have completed by the end of the day.

* Commit and push your work to GitLab (For help see :ref:`git-reference`)
* Create a Merge Request and have the teacher and classmates review your changes
