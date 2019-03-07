:orphan:

.. _intellij:

=======================
Configuration: IntelliJ
=======================

IntelliJ
--------

IntelliJ is the IDE we will be using throughout this course. There are many IDEs with various pros, and cons, but we will only talk about IntelliJ throughout this class.

Some benefits of using an IDE instead of a text editor
    * Configurable Runtimes 
    * Testing Runtimes
    * Environment Variables
    * Dependency Management
    * Build Tools
    * Project Structure
    * Viewable External Libraries
    * Refactor Checking

In this article we will be configuring IntelliJ to run Java 8, which is the first step to being able to use some of the benefits listed above.

Configuration
-------------

To get our project to run in IntelliJ we will need to configure a few things:

* Set the Project SDK, Project language level
* Manage compiler output
* Mark main, and test directories

In later sections of this class we will be working with gradle, and most of this will be pre-configured for you, but it's still necessary to know what is happening.

Project SDK & Project Language Level
++++++++++++++++++++++++++++++++++++

SDK stands for Source Development Kit, we have to tell InteliJ which version of Java we are using, and where it exists on the machine.

* Open the Project Structure

.. image:: /_static/images/unit-tests/project-structure.png

* Set our Project SDK

.. image:: /_static/images/unit-tests/project-sdk.png

* Set our Project language level

.. image:: /_static/images/unit-tests/project-language-level.png

Compiler Output
+++++++++++++++

Every time our project compiles output is generated. IntelliJ needs to know where to store this output.

* Create a new folder for our compiler output

.. image:: /_static/images/unit-tests/new-out-folder-location.png

.. image:: /_static/images/unit-tests/new-out-folder.png

* Set our Project compiler output

.. image:: /_static/images/unit-tests/project-compiler-output.png

Main and Test Directories
+++++++++++++++++++++++++

IntelliJ will allow you to have complex projects, and use complicated file structures, but it needs to know which directories in your project is the source root, and where your tests reside.

* Mark the main directory as Source Root

.. image:: /_static/images/unit-tests/main-directory-as-source-root.png

* Mark the test directory as Test Sources Root

.. image:: /_static/images/unit-tests/test-directory-as-test-root.png