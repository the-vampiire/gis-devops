:orphan:

.. _postgres:

========================
Installation: PostgreSQL
========================

We will be using PostgreSQL (PSQL, Postgres) throughout this class. PSQL will store our databases, database users, tables, and records. PSQL will also serve as our primary data store for the project we will be building throughout this class.

We will interface with PSQL in multiple ways:

* Through the PSQL interactive terminal
* Through a GUI (PGAdmin, PeSequel, phpMyAdmin, etc)
* Through our web applications (Our Java web applicatoins will access PSQL through Spring data: JPA)

Before we can work with PSQL we first need to install PSQL.

Installation Steps: Mac
-----------------------

1. Go to `https://postgresapp.com/ <https://postgresapp.com/>`_
2. Download and open the file
3. Add postgres to your $PATH

Add postgres to your $PATH
--------------------------

* Open a terminal
* Open your ``.bash_profile`` in an editor like nano::

    $ nano ~/.bash_profile

* Paste in this text to your ``~/.bash_profile``::

    # add postgres and it's related commands to path
    export PATH="/Applications/Postgres.app/Contents/Versions/latest/bin:$PATH"

* Save your ``~/.bash_profile`` file
* Open a new terminal and run ``$ postgres --version``
