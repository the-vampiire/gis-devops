:orphan:

.. _postgres-walkthrough:

=======================
Walkthrough: PostgreSQL
=======================

Setup
-----

* Check that you have postgres installed by typing ``$ postgres --version`` into your terminal
* Check that you have docker installed by typing ``$ docker --version``
* Check that you have a running docker container for psql by typing ``$ docker ps -a``

If you are missing any of the three things above please fix them before we continue with the walkthrough.

* :ref:`postgres`
* :ref:`docker`
* :ref:`docker-psql`

Concepts
--------

* Database
* Schema
* Table
* Record
* CRUD -- Create, Read, Update, Delete
* Primary Key
* Foreign Key

Let's Run Some Postgres Commands
--------------------------------

* Connect to PSQL interactive terminal ``psql -h IP_ADDRESS -p PORT -U USER_NAME -d DATABASE_NAME``

Create a Database
^^^^^^^^^^^^^^^^^

A database is a collection of schemas and tables. A database may have any number of schemas, and those schemas may have any number of tables. Tables may, or may not have various relationships with other tables. One database will not have any relationship with any other database. In essence databases are isolated. One database cannot communicate with any other databases.

* ``CREATE DATABASE sports;``
* ``\l`` -- command to list all databases
* ``\c sports`` -- command to connect to the new sports database

Create a Schema
^^^^^^^^^^^^^^^

A schema is a collection of tables, relationships, data types, functions, and operators. Schemas are not isolated like databases are. One schema can reference other schemas in the same database.

Other benefits of schemas include:
    * Multiple users can access the same database without conflict
    * Better organization of databases
    * Third party application management

Let's create a schema, and view it.
    * ``CREATE SCHEMA baseball;``
    * ``\dn`` -- command to list all schemas on the currently connected database

Create a Table
^^^^^^^^^^^^^^

A table has columns that contain a column name, and a data type. Some columns have constraints which allow us to add some additional rules to our table. ``Not null``, ``Unique``, ``Primary Key``, and ``Foreign Key`` are examples of constraints. Using the ``Not null`` constraint requires that column must contain data when a record is being inserted into the table. 

Each individual piece of data added to a table is called a record. 

* ``CREATE TABLE baseball.teams (id SERIAL, name varchar(50), league varchar(25), PRIMARY KEY (id));`` -- create a new table on the baseball schema with three columns: id, name, and league
* ``\dt baseball.*;`` -- command to list all tables on the baseball schema

We are using the PRIMARY KEY constraint on the id column. A **primary key** is the unique identifier for a record. In this example our Primary Key is the SERIAL type. The SERIAL type is incrementing integers starting at 1. Every time a new record is added to this table, the next integer will be assigned as it's primary key.

Create Record - Insert Into
^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can create records, by inserting them into a table.

* ``INSERT INTO baseball.teams(name, league) VALUES ('St. Louis Cardinals', 'National');``
* ``SELECT * FROM baseball.teams;``

We can insert as many records as we want with one INSERT INTO statement.

* ``INSERT INTO baseball.teams(name, league) VALUES ('Washington Nationals', 'National'), ('Chicago Cubs', 'National'), ('New York Mets', 'National'), ('New York Yankees', 'American');``

Read Record - Select
^^^^^^^^^^^^^^^^^^^^

* ``SELECT * FROM baseball.teams;`` -- SELECT all the columns from all the teams
* ``SELECT name FROM baseball.teams;`` -- SELECT only the name column from all the teams
* ``SELECT * FROM baseball.teams WHERE league='National';`` -- SELECT all the columns from all the teams in the 'National' league
* ``SELECT name FROM baseball.teams WHERE league='National';`` -- SELECT only the name column from teams in the 'National' league

Alter Table
^^^^^^^^^^^

A table can be changed. We can add, or drop columns, or change constraints.

* ``ALTER TABLE baseball.teams ADD COLUMN dvsn VARCHAR(10);`` -- Add the 'dvsn' column to baseball.teams
* ``SELECT * FROM baseball.teams;``
* ``ALTER TABLE baseball.teams DROP COLUMN IF EXISTS dvsn``; -- Let's drop that change, and rename it division
* ``SELECT * FROM baseball.teams;``
* ``ALTER TABLE baseball.teams ADD COLUMN division VARCHAR(10);`` -- Add the 'division' column to baseball.teams
* ``SELECT * FROM baseball.teams;``

Update Record(s)
^^^^^^^^^^^^^^^^

We can update the individual records in our table with the UPDATE statement. Each UPDATE statement must contain a SET statement which defines which column(s) will be updated, and a WHERE clause which defines which records will be updated.

* ``UPDATE baseball.teams SET division='Central' WHERE name='St. Louis Cardinals';`` -- Update the record that matches the WHERE clause
* ``SELECT * FROM baseball.teams;``
* ``UPDATE baseball.teams SET division='East' WHERE name='Washington Nationals' OR name='New York Mets' OR name='New York Yankees';``
* ``SELECT * FROM baseball.teams;``
* ``UPDATE baseball.teams SET divison='Central' WHERE name='Chicago Cubs';``
* ``SELECT * FROM baseball.teams;``

.. warning::

  Any record that matches the WHERE clause will be updated!

Delete Record(s)
^^^^^^^^^^^^^^^^

We can also delete individual records. Before we do let's add a team that no longer plays in the MLB, so we can delete them.

* ``INSERT INTO baseball.teams(name, league) VALUES ('St. Louis Brown Stockings', 'National');`` -- Adding a team we are about to delete
* ``SELECT * FROM baseball.teams;``
* ``DELETE FROM baseball.teams WHERE id=6;`` 
* ``SELECT * FROM baseball.teams;``

.. warning::

    Any record that matches the WHERE clause will be deleted! Since we deleted by the id, which is a primary key we are ensuring that only 1 record is affected.

Foreign Key
^^^^^^^^^^^

We recently learned that a Primary Key is the unique identifier for one record in a table. A Foreign Key is a reference to another record on another table.

What if we were to create a new table called baseball.players and filled it with various MLB players. It would be nice to include data about the team the player currently plays for. However in the MLB players are traded, retire, enter Free Agency, etc, and their team affiliations change. Instead of changing all of that data for each player every time a team change happens we should use the data that already exists in the baseball.teams table.

We can do this by creating a reference to the baseball.teams table within our new table.

* ``CREATE TABLE baseball.players (id SERIAL PRIMARY KEY, team_id INTEGER REFERENCES baseball.teams(id), first_name VARCHAR(50), last_name VARCHAR(50));``
* ``SELECT * FROM baseball.players;``
* ``INSERT INTO baseball.players (team_id, first_name, last_name) VALUES (1, 'Albert', 'Pujols'), (1, 'Yadier', 'Molina'), (5, 'Alex', 'Rodriguez');``
* ``SELECT * FROM baseball.players;``

Now we can join these tables together, and view it all at the same time.

* ``SELECT * FROM baseball.teams, baseball.players WHERE baseball.teams.id=baseball.players.team_id;`` -- view team info first
* ``SELECT * FROM baseball.players, baseball.teams WHERE baseball.players.team_id=baseball.teams.id;`` -- view player info first
* ``SELECT * FROM baseball.players, baseball.teams WHERE baseball.players.team_id=baseball.teams.id AND baseball.players.team_id=1;`` -- only select players on the St. Louis Cardinals

Albert Pujols signed with the Los Angeles Angels after playing for the St. Louis Cardinals, so we need to change his ``team_id``.

* ``INSERT INTO baseball.teams(name, league, division) VALUES ('Los Angeles Angels', 'American', 'West');``
* ``SELECT id from baseball.teams WHERE name='Los Angeles Angels';``
* ``SELECT id from baseball.players WHERE first_name='Albert' AND last_name='Pujols';``
* ``UPDATE baseball.players SET team_id=7 WHERE id=1;``

Now when we select all the players on the Cardinals roster we don't see Albert Pujols, because his ``team_id`` changed.

* ``SELECT * FROM baseball.players, baseball.teams WHERE baseball.players.team_id=baseball.teams.id AND baseball.players.team_id=1;``

When we look at all players with team info we can see the data associated with Albert Pujols has changed. Albert Pujols is now refrencing the Los Angeles Angels.

* ``SELECT * FROM baseball.players, baseball.teams WHERE baseball.players.team_id=baseball.teams.id;``

When a column references another tables PRIMARY KEY we call it a FOREIGN KEY. In the example we have worked on so far ``team_id`` on the baseball.players table is a Foreign Key that references the Primary Key on the baseball.teams table.

Resources
---------

We have barely touched the surface of Postgres, or SQL. You can find more information by reading the `Postgres documentation <https://www.postgresql.org/docs/>`_

We have covered everything that you will need to know for this class, but if you are hungry for more you should research JOIN statements.