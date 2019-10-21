:orphan:

.. _eslint-airwaze-walkthrough:

===========================
Walkthrough: ESLint Airwaze
===========================

Concept
=======

Linting
-------

We use linting to reduce the number of code smells we create, and to bring our code to a standard that other programmers in our group will be able to understand, and work with.

Terms:
    - **Ruleset**: a pre-defined set of rules, or conventions our code will follow.
    - **Lint**: code that does not follow our ruleset.
    - **Linting**: the process of removing lint.
    - **Linter**: a tool that scans our code, and highlights lint.

It is possible to follow a ruleset without using a linter. However, we are people and inevitably will make mistakes. We use linters to manage our ruleset, and to scan our code to ensure we are following rulesets. Many linters will run automatically, or are built into IDEs to make spotting lint as easy as possible. Using a linter allows us to focus on solving a problem instead of focusing on the ruleset.

Most linters will tell you what rule you have broken, and where that mistake was made. It is still up to us as the programmer to make the changes pointed out by our linter.

Ruleset
-------

We will be using the `Airbnb JavaScript Style Guide <https://github.com/airbnb/javascript>`_.

There are many different rulesets, and they have a few different names: style guide, ruleset, conventions, etc. They all refer to the same thing a pre-defined set of conventions/rules/styles we will follow when we write our code. Typically, before any code is commited it needs to follow all rules in the ruleset. Teams are responsible for defining their rulesets, many teams will adopt a widely used ruleset, and then modify it to meet their needs.

Common Rules addressed by Rulesets:
    - Whitespace: Spaces, or Tabs? How many? What should be indented?
    - Variable Naming Standards: CamelCase, pascalCase, snake_case?
    - Object Intialization: New keyword?? Literal syntax?
    - Brackets: Same line as code, or on their own line?
    - Comments: Single line? Multi line? Docstrings?
    - Line length: how long can a line be?

Setup
=====

We will be setting up ESLint for the Airwaze project we worked on in Week 1 of this class.

Tasks:
    - Create a new branch
    - Install NPM
    - Initialize NPM
    - Install ESLint
    - Initialize ESLint
    - Configure which JavaScript file(s) to lint
    - Configure ruleset to match our team's conventions

Create a New Branch
-------------------

First we will need to create a new branch for our work. From either the branch you created last week, or the ``eslint-starter`` branch, create a new branch called ``eslint-solution``.

Install NPM
-----------

On mac we will install NPM with ``$ brew install node``.

NPM stands for Node Package Manager, it is a package manager for JavaScript, and comes with Node.js. This package manager will allow us to download JavaScript modules from the NPM Registry. One of those packages is ESLint.

You can check that NPM installed correctly with: ``$ npm --version``.

You can check that Node.js installed correctly with: ``$ node --version``.

NPM is a very powerful tool. If you want to learn more about backend web development with JavaScript, or using a front-end framework like Angular.js, or React.js you will almost certinaly use NPM. However, NPM, and Node.js are outside the scope of this class. We will only be using NPM as a means of installing, and configuring ESLint.

Initialize NPM
--------------

After installing NPM we need to initialize NPM inside our project so that we can use it in our project. Upon initializing NPM inside our project NPM will prompt us for some information to configure NPM for this project.

From the root of your airwaze project (/airwaze) run ``$ npm init``.

We will be accepting all of the defaults except for test command, we are going to enter how we are testing, and the file to be tested. You can enter in information for the various prompts, but they are primarly metadata.

Navigate through the following prompts:
    - package name: (airwaze)
    - version: (1.0.0)
    - description: 
    - entry point: 
    - **test command:** eslint src/main/resources/static/js/script.js
    - git repository: (.git)
    - keywords: 
    - author: 
    - license: (ISC)
    - About to write. Is this OK?: (yes)

Once you hit enter on that last prompt it will create a new file for you called ``package.json``. This is the file that manages how your project interacts with NPM. The contents of the ``package.json`` file match whatever we entered in the prompts when we ran ``$ npm init``. You can change the package.json file, and it will change how NPM behaves in this project. The only thing we needed to set here was under the ``"test"`` key. We set it to run ``eslint``, and to check the file at ``src/main/resources/static/js/script.js`` a location relative to where we ran ``$ npm init``. If you initialized npm in a different directory your path will be different.

Install ESLint
--------------

We now need to use NPM to install ESLint, and ESLint-plugin-import.

From the same directory you initialized NPM in run the following commands:
    - ``$ npm install eslint --save-dev``
    - ``$ npm install eslint-plugin-import --save-dev``
    - ``$ npm install eslint-config-airbnb-base --save-dev``

The ``$ npm install`` command will create a new directory called ``node_modules`` and will install the packages, and their dependencies into the ``node_modules`` directory. This is the code that runs when we use ESLint. The ``--save-dev`` option saves the installed packages inside devDependencies indicating these packages are only needed while the project is being developed, and they are not needed in production.

You can change into the ``node_modules`` directory, and see all of the code that is used to run ESLint.

.. _configure_eslint:

Initialize ESLint
-----------------

Now that ESLint is installed in this project, we still need to intialize ESLint for this project.

We do that by running ``$ ./node_modules/.bin/eslint --init`` and selecting:
  - How would you like to use ESLint?: To check syntax, find problems, and and enforce code style
  - What type of modules does your project use?: None of these
  - Which framework does your project use?: None of these
  - Where does your code run?: Browser
  - What format do you want your config file to be in?: JavaScript

Similarly to NPM init, ESLint init prompts us and based on our answers creates a file that holds our preferences, and is used when we use ESLint.

The file created is a hidden file (it starts with a "."), and is called ``.eslintrc.js``. This file holds the information ESLint uses when Linting our code.

Configure ESLint
----------------

If you followed to this point you are almost done. However, let's double check that we've configured NPM, and ESLint correctly.

We have two files, and one directory we need to check ``package.json``, ``.eslintrc.js`` are the files and ``node_modules`` is the directory.

``package.json`` contains all the data NPM needs to function. 

Inside ``package.json`` we have a "scripts" section that contains the type of "tests" we are running: ``eslint`` and the file we are testing: ``src/main/resources/static/scripts/js/script.js``. 

``package.json`` defines what tool we are using for testing (ESLint), and the file we are testing (script.js).

Verify that your ``package.json`` file has the following "scripts" section:

.. sourcecode:: javascript
   :caption: package.json

   "scripts": {
       "test": "eslint src/main/resources/static/js/script.js"
   }

You need to point your "test" key to the relative location of your script.js file.

``.eslintrc.js`` contains all the data ESLint needs to function, the version of JavaScript (es6), that code is being run in the browser, the ruleset being used (airbnb-base), and more.

``node_modules`` contains all the actual code needed by ESLint to function. We don't need to check anything in this folder, but it's nice to know why it is here.

.. _configure_airbnb_ruleset:

Configure Airbnb Ruleset
------------------------
You need to tell ESLint to use the Airbnb ruleset. Change the ``extends`` property to have the value ``airbnb-base``.

.. sourcecode:: javascript
   :caption: .eslintrc.js

   "extends": "airbnb-base",

Override a Rule
^^^^^^^^^^^^^^^
As a final step before we run ESLint we should override one rule in the Airbnb ruleset. We have been using 4 spaces as whitespace in our JavaScript files throughout this class, and Airbnb's ruleset only allows for 2 spaces as whitespace.

We need to override this rule, because our specific team uses a different rule. Luckily we can change our ruleset any way we see fit.

Open the ``.eslintrc.js`` file. Near the bottom you should have an empty "rules" section. Modify that section so that it looks like this:

.. sourcecode:: javascript
   :caption: .eslintrc.js

   rules: {
    "indent": ["error", 4]
   },
   
This rule modifies the "indent" rule from the Airbnb ruleset, instead of the default value we are hard coding the value to be 4. So our ESLinter will give an "error" when the level of indentation does not match 4 spaces.

Testing with ESLint
===================

Now that we have installed, intialized and configured both NPM, and ESLint it's time to do some linting!

Run ``$ npm test`` the output should give us quite a few errors, and warnings.

Errors, and warnings are lines of code that break one of our rules. You can read the Airbnb style guide to figure out why they've decided some things are warnings, and some things are errors. ESLint is configurable, we can create, or modify rules and change them to either be errors, or warnings to meet the needs of our style guide.

Fix one issue at a time, save your file, and run ``$ npm test`` again

.gitignore & Commit
===================

After you have fixed all of the errors we will want to commit our work. But before we do we need to update our ``.gitignore``.

We added two new files (package.json, .eslintrc.js), and one new directory (node_modules). It's a good idea to commit package.json, and .eslintrc.js because they contain configuration information, but we don't want to commit dependency code.

Open ``.gitignore`` and add the line ``*node_modules`` to it.

Now stage, commit, and push your changes.

Bonus
=====

Setup VisualStudioCode to run ESLint for you. See :ref:`vscode-eslint`

Resources
=========
* `ESLint Rules <https://eslint.org/docs/rules/>`_
