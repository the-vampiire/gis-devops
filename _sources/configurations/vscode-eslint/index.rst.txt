:orphan:

.. _vscode-eslint:

==========================================
Configuration: Visual Studio Code - ESLint
==========================================

It's totally fine to view linting errors in the console, but we can also use editors to alert us of these issues as we write the code. We are going to setup VisualStudio Code to "auto lint" using our defined linting rules. After we set this up, we won't have to save and re-run ``$ npm test``.

* Open Visual Studio Code (if you don't have it, you should use ``$npm test`` to lint your code)
* Open your Airwaze project

  * You only have to open the /airwaze-project/src/main/resources folder, because that is where the js and eslint files are
  
* Click on extensions (the squareish icon in the left menu)
* Enter eslint into the search box
* Click install
* Click reload

  .. image:: /_static/images/vs-code-eslint-plugin.png

* Now go to your ``script.js`` files and look for red underlined code
* Hover over the code for an explanation of the error

  .. image:: /_static/images/vs-code-show-errors.png