:orphan:

.. _openlayers-es2015-studio:

===================================
Studio: OpenLayers ES2015 Workshop
===================================

This activity gives you practice with JavaScript, ES2015, Webpack, and OpenLayers


Getting Started
===============

Download Project
^^^^^^^^^^^^^^^^

#. Open the `OpenLayers gitbook <https://openlayers.org/workshop/en/>`_ in your browser.
#. Download latest version of the OpenLayers Workshop.
  
   * Example: v6.0.0-beta.en.2
   * Download the zipped folder version (NOT the source files)

#. Extract the folder and move it to a folder where you normally keep your projects.
#. In a terminal
  
   * Navigate to the OpenLayers Workshop folder you just extracted and moved
   * Run ``git init`` to setup a get repository (this will only be a local repo until you have connected it to a remote)

#. Add a ``.gitignore`` file to the root of your project.

   * In that file add this: ``**/node_modules``
   * This makes git ignore the node_modules folder

Configure NPM Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^

#. Update ``eslint`` version in ``devDependencies`` in ``package.json``

   * Update to: ``"eslint": "^6.2.1"``

#. Add these to ``devDependencies`` in ``package.json``

   * ``"eslint-config-airbnb-base": "^14.0.0"``
   * ``"eslint-plugin-import": "^2.18.2"``

#. Run ``npm install``

   * For the sake of this example, ignore the "npm audit warnings".

Configure ESLint and Airbnb Ruleset
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Follow the :ref:`ESLint initialization and config instructions. <configure_eslint>`

   * One key difference is that ``test`` command should be changed to only "lint" ``main.js``
   * ``"test": "eslint main.js"``

2. Follow the :ref:`Airbnb ruleset config instructions <configure_airbnb_ruleset>`

Make Sure it Starts
^^^^^^^^^^^^^^^^^^^

1. Run ``npm start``
2. View site at ``http://localhost:3000``


Your Tasks
==========

Follow a serious of instructions in the `OpenLayers gitbook <https://openlayers.org/workshop/en/>`_. The OpenLayers Workshop has a series of
examples. Your task is to get the examples working locally and then make them pass the Airbnb ESLint ruleset.

1. Start with the "Basics" section in the left menu and continue down.

2. Get each example to work locally, by reviewing and copying the code.
   * Each example will require you to edit ``main.js`` and ``index.html``
   * You can commit your work in between each example or you can create different branches

3. Make the example code pass the Airbnb ruleset.

   * Run ``npm test`` in terminal or use the visual linting provided by Visual Studio Code.
   * Update the code to pass the ruleset.
   * Make sure the example code still works after you make your edits. It's better to check frequently instead of after a large number of changes.


Turning In Your Work
====================

If you don't complete each of the tasks, turn in as much as you have completed by the end of the day.

* Commit and push your work to GitLab
* This will require that you creat a new repository in your GitLab account

  * Then you will have to add the URL for that new repository as a remote to your local repository
  * Finally you will be able to push your code up to your new remote repository
