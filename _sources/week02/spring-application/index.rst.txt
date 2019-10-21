:orphan:

.. _week2_spring-application:

=============================================================
Project Week2 Setup: Spring, Git, GitLab, PostGIS, & IntelliJ
=============================================================

Before we can begin working on the requirements for this week, we will need to first setup the tools we will be using throughout this week.

Tasks:
    - Create Spring Project
    - Open Project in IntelliJ
    - Configure Project Dependencies in Gradle
    - Intialize a New Git Repository
    - Connect our local git repository to a remote repository on GitLab
    - Setup PostGIS
    - Create Databases, and Database users
    - Configure Spring Data

Create Spring Project
---------------------

We will be creating a Spring project from scratch today! Luckily for us Pivotal, the owners of Spring, have tried to make it as easy as possible to create a new Spring project. 

In your browser navigate to `start.spring.io <https://start.spring.io>`_.

From the Spring Initializr we have some options to create a ready to go spring application!

You will need to select:
    - Project: *Gradle*
    - Language: *Java*
    - Spring Boot: *2.1.X*
    - Project Metadata Group: *org.launchcode*
    - Project Metadata Artifact: *zikaDashboard*
    - Project Metadata Name: *zikaDashboard*
    - Project Metadata Description: *CDC Zika Dashboard*
    - Project Metadata Package Name: *org.launchcode.zikaDashboard*
    - Project Metadata Packaging: *Jar*
    - Project Metadata Java Version: *8*
    - Dependencies: *Web*
    - Dependencies: *Thymeleaf*
    - Dependencies: *JPA*
    - Dependencies: *DevTools*

Double check that you have filled out the information correctly, and then click Generate Project. This will prompt your browser to download a file called zikaDashboard.zip, go ahead and download it, and take notice of where it is downloaded. Most machines default to a Download directory off of your Home directory.

We will want to move the zikaDashboard.zip file to wherever you typically store your projects, and then unzip the file.

Your command will likely be different, but one example of moving the zikaDashboard.zip file to the projects folder can be completed with the following command.

.. sourcecode:: console
   :caption: Move & unzip zikaDashboard.zip

   $ mv /home/paul/Downloads/zikaDashboard.zip /home/paul/projects/
   $ cd /home/paul/projects
   $ unzip zikaDashboard.zip

Upon unzipping the file, we will have a new folder called zikaDashboard. Looking into the folder you will see a familiar project structure for Spring projects.

Open Project in IntelliJ
------------------------

Now that we have a directory with our Spring application in it, let's go ahead and open this project with IntelliJ.

From IntelliJ Open ``build.gradle`` as a new project, make sure use auto-import is selected!

After opening your project look around, it should look very familiar to the other projects we have worked on in this class.

Configure Project Dependencies
------------------------------

In creating our Spring project we added four dependencies directly from Spring: Web, Thymeleaf, JPA, and DevTools.

However, we have 2 additional dependencies we will need: a PSQL Driver, and Hibernate. These two tools are not managed by Spring, and cannot be loaded through the Spring Initializr, so we will have to manually include their dependencies.

Open the ``build.gradle`` file in your new project. You will need to add two things.

First we will have to configure a new buildscript section of this file, that will add our new dependencies to our classpath.

At the top of your file add the following code block.

.. sourcecode:: console
   :caption: Add buildscript

   buildscript {
     repositories {
       mavenCentral()
     }
     dependencies {
       classpath(group: 'org.postgresql', name: 'postgresql', version: '42.1.4')
       classpath(group: 'org.hibernate', name: 'hibernate-spatial', version: '5.1.0.Final')
     }
   }

Next we will have to add those dependencies to our project.

Towards the bottom of your file you should find a separate dependencies section. It should already have implementation, runtimeOnly, and testImplementation statements, we don't want to change those. 

We just want to add the following three statements.

.. sourcecode:: console
   :caption: Add dependencies

   dependencies {
     ...
     compile(group: 'org.postgresql', name: 'postgresql', version: '42.1.4')
     compile(group: 'org.hibernate', name: 'hibernate-spatial', version: '5.1.0.Final')
     compile(group: 'com.bedatadriven', name: 'jackson-datatype-jts', version: '2.4')
   }

After you add these statements you should notice IntelliJ displays a loading symbol as it downloads, and installs these dependencies into your project. It should only take a second.

You can see these files by looking into the External Libraries directory, and looking for ``Gradle:org.hibernate:hibernate-spatial:5.1.0.FINAL``, and ``Gradle:org.postgresql:postgresql:42.1.4``. They will be among the other dependencies we loaded from the Spring Initializr.

Intialize a New Git Repository
------------------------------

You may notice that Spring Initializr created a ``.gitignore`` file for you. However, it is not able to initialize a new git directory for you.

You will need to initialize a new git directory with ``$ git init``.

Look over the .gitignore file and determine if there is anything else you may need to add to it.

Connect to a Remote Repository
------------------------------

You will want to connect your local git repository to a remote repository. To do this you will need to create a new project on your GitLab account. After doing that you can connect the two from your command line by typing ``$ git remote add origin <url_to_remote_git_repo>``.

After you connect your local to your remote, you may want to stage, commit, and push. This will synchronize your local, and remote repositories so that your remote repository will contain your starter project.

Setup PostGIS
-------------

We are going to need a PostGIS database. You are more than welcome to use a PostGIS database we used for previous projects.  You will need to ensure the PostGIS container is running, and listening to requests on port 5432.

If you need a refresher on creating PostGIS containers checkout out the `Docker PostGIS installation <../../installations/docker-postgis/>`_ article.

Create Databases & Users
------------------------

Within your PostGIS container we will need to create 2 new databases, and 2 new users from the PSQL CLI.

In order to access the database through the PSQL CLI. You will have to check that the PostGIS container is running ``$ docker ps``. Make sure that you only have one database container running, since PSQL listens on port 5432, if you have multiple containers active at the same time unexpected things could happen.

Once your container is running you can login to the PSQL CLI with ``$ psql -h 127.0.0.1 -U <psql_user> -d postgres``.

You will then need to create 2 databases for our zika project, and 2 users with full access to those databases.

Configure Spring Data
---------------------

Now that our databases are setup, and we have our users, we can configure Spring Data.

We will need to create, or add to the application.properties file. This file lives in the src/main/resources folder.

You will want to add this to it:

.. sourcecode:: java
   :caption: application.properties

   spring.datasource.url=jdbc:postgresql://${APP_DB_HOST}:${APP_DB_PORT}/${APP_DB_NAME}
   spring.datasource.username=${APP_DB_USER}
   spring.datasource.password=${APP_DB_PASS}
   spring.jpa.hibernate.ddl-auto=create
   spring.datasource.testWhileIdle=true
   spring.datasource.validationQuery=SELECT 1
   spring.jpa.show-sql=true
   spring.jpa.hibernate.naming-strategy=org.hibernate.cfg.ImprovedNamingStrategy
   spring.jpa.properties.hibernate.dialect=org.hibernate.spatial.dialect.postgis.PostgisDialect
   spring.jpa.properties.hibernate.temp.use_jdbc_metadata_defaults= false

.. note::
   
   The example above is using Environment Variables. If you need help check out the `environment variables <../../configurations/environment-variables-intellij/>`_ article.

Since ``spring.jpa.hibernate.ddl-auto`` is set to ``create`` this will create any Models with the @Entity annotation, and will run the import.sql script at startup.

Let's configure the import.sql script. Create a new file in the same location as application.properties and add this to it:

.. sourcecode:: sql
   :caption: import.sql

   BEGIN;

   CREATE EXTENSION IF NOT EXISTS postgis;
   CREATE EXTENSION IF NOT EXISTS postgis_topology;
   CREATE EXTENSION IF NOT EXISTS fuzzystrmatch;
   CREATE EXTENSION IF NOT EXISTS postgis_tiger_geocoder;
   CREATE EXTENSION IF NOT EXISTS unaccent;

   -- COPY report() FROM '/tmp/report.csv' DELIMITER ',' CSV HEADER;

   COMMIT;

As a final step let's create a new Report class, and add the @Entity annotation so we can see hibernate create the report table for us!

Add a ``models`` directory in ``main/java/org/launchcode/zikaDashboard``.

Inside the new ``models`` directory add a new Java class called ``Report.java``.

Inside that file add this code:

.. sourcecode:: java
   :caption: Report.java

   @Entity
   public class Report {

       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;

       // required Hibernate constructor
       public Report() {}
   }

The Report class is very empty so far. It just contains an id, and an empty constructor that is required for Hibernate to map records to objects.

Make Sure it Works
------------------

Now that we have configured our project let's run our application. Select ``bootRun`` from the gradle menu, or create a new Gradle runtime with the task ``bootRun``.

.. note::
   
   Don't forget you will need to add `environment variables <../../configurations/environment-variables-intellij/>`_ to your runtime configuration!

If you don't get any errors you should see the tomcat logs, if you scroll up in the logs you can see that Spring Data ran your import.sql script, and it created a new report table in the database.

It would be a good idea to login to the PSQL CLI and make sure the report table was created successfully. The table should exist, but only have one column called id, and it won't have any records yet.

If you get errors in your log, or don't see the report table, read over these instructions again, talk to your fellow classmates, and ask the instructor for help.

Commit
------

After everything has been configured it would be a good idea to add, commit, and push your work to GitLab.

`Back to week2! <../project/>`_