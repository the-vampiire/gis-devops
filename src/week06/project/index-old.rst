.. _week6_project:

===================================================
Week 6 - Project Week: Zika Mission Control Part 3
===================================================

Project
=======

Mission Briefing 3 : `pdf <../../_static/images/zika_mission_briefing_3.pdf>`_

Overview
========

The Zika Dashboard that your team has built is growing in popularity. In fact, the application is strained under increased load from CDC Scientists. Your team needs to find a way for you app to scale under increased load. The goals of this mission are as follows:

1. Deploy your application remotely using Amazon Web Services.
2. Configure a Jenkins job to automatically build and test your code. Jenkins should save an executable JAR file to S3 if all of the relevant checkes pass.

Requirements
============

To complete this project, your app should meet the following requirements:

1. Your application is deployed via AWS at a live URL
2. You use CentOS for your EC2 instances (details below)
3. Setup a LaunchConfiguration to spin up new Web App EC2 instances
4. You setup a VPC for your Web app, RDS, and Elasticsearch
5. Elasticsearch is hosted on an EC2 inside your VPC
6. Your VPC can Auto Scale under load
7. Jenkins watches your repo pushes a release to S3 if all of the following pass:

   * All tests pass

8. ESLint has no warnings

New Stuff
=========

There will be a few differences in this project compared to previous week's studios/project.  You may want to copy over certain changes instead of merging `Week 6 Starter <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/tree/week6-starter>`_. NOTE that the starter branch only contains config, property, and aws scripts. The starter branch does not contain a solution for week 4.

Review the changes by looking at this branch comparison: `week4 compared to week6 <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/compare/week4-starter...week6-starter>`_

Specific Changes
----------------

1. For your **Web App** EC2 instances you will be using the ``CentOS`` operating system instead of ``Ubuntu``
2. Using Spring Data 2.0.2 (dependencies have been updated in build.gradle)
3. When running locally you will be running **Elasticsearch** inside a **Docker** instance (details below)
4. When running in the cloud you will be running **Elasticsearch** on a ``Ubuntu`` EC2 instance

Milestones
==========

This week has us deploying our project to AWS. We recommend setting three milestones for yourself this week.

1. Setup and run your project locally with Docker.
2. Setup and configure your VPC on AWS properly.
3. Access your application via the live URL.

Milestone 1 - Setup Locally
---------------------------

So far in this class we have been running Elasticsearch as an embedded instance inside of our Tomcat server. When we move to the cloud we will be moving Elasticsearch to it's own EC2, and our web application will have to make requests to a server to function properly. This requires us to make some changes to our code.

We want to make these changes in our local development environment to make it easier to troubleshoot. Our goal is to simulate this new process locally before moving it to the cloud.

We will be using Docker to do this. Locally we will be running elasticsearch in it's own Docker container, and our web application will make elasticsearch requests to this container. The functionality of our project will not change, how our web app communicates with elasticsearch will change. This new way more accurately reflects what is happening with our web app in AWS.

To get our project up and running locally we will need to:

1. Stop the elasticsearch service in brew
2. Download docker & create new container
3. Bring in the new week6-starter code
4. Update new envrionment variables
5. Run our application
6. Run our tests

1) Stop elasticsearch in brew
_____________________________

Our computer will throw a fit if we have to different versions of elasticsearch running at the same time, and listening on the same port. It would cause some issues that would be difficult to troubleshoot. So to kick things off let's turn off the brew service we were using last week.::

  $ brew services list
  $ brew services stop elasticsearch
  $ brew services list

This has stopped the version of elasticsearch we installed and ran through brew.

2) Download docker
__________________

* Download docker installer from `here <https://store.docker.com/editions/community/docker-ce-desktop-mac>`_
* That will install docker as a service and an application that will run everytime your computer starts. (look for the whale icon in your menu bar at the top of your mac)
* Now you can run ``$ docker`` commands in your terminal. Like the one below. (this runs a docker instance that contains Elasticsearch)
* The connection information for the docker version of Elasticsearch is contained in the comments of `this file <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week6-starter/src/main/resources/application.properties>`_

Create the container (NOTE: An id will be returned)::

  $ docker create -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:5.6.0
  63918ba7994482d94bdaf7bbc2005d91e0ac2f02a88039ebe2521ed9d9e8e8b8 <----- this is returned after the above command, it's id of the container that is created COPY THIS SOMEHWERE

This command creates a new docker container, tells it to listen on ports 9200, and 9300, and includes the image of what exists in the container. In this case that is elasticsearch 5.6.0.

Start the container::
  
  $ docker start 63918ba7994482d94bdaf7bbc2005d91e0ac2f02a88039ebe2521ed9d9e8e8b8


You can restart or stop the container as well::

  $ docker restart 63918ba7994482d94bdaf7bbc2005d91e0ac2f02a88039ebe2521ed9d9e8e8b8
  $ docker stop 63918ba7994482d94bdaf7bbc2005d91e0ac2f02a88039ebe2521ed9d9e8e8b8

To view your current docker containers::

  $ docker ps -a

This command will print out all containers and their current status.

.. note::

  The error ``None of the configured nodes are available`` can be caused by starting up your Web App before Elasticsearch is running. This can also happen if you restart your Elasticsearch while your Web App is running.

3) Bring in week6-starter code
______________________________

Now that we have installed, and created a new Docker container for our elasticsearch, we need to bring in the new changes that will configure our application to work with this new containerized version of Elasticsearch.

There will be a few differences in this project compared to previous week's studios/project.  You may want to copy over certain changes instead of merging `Week 6 Starter <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/tree/week6-starter>`_. NOTE that the starter branch only contains config, property, and aws scripts. The starter branch does not contain a solution for week 4.

Review the changes by looking at this branch comparison: `week4 compared to week6 <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/compare/week4-starter...week6-starter>`_

.. note::

  One of the changes updates our spring plugin from version 1.5.2 to 2.0.2. This changes the gradlewrapper of our project, which is what defines the gradle commands we can run. Last week we used the gradle command bootRepackage to build our .jar file. This week we will be using the gradle command bootJar to build our .jar file. 

4) Update new envrionment variables
___________________________________

One of our changed files is application.properties.

At the bottom of this file there are three new application level variables being set:
1. elasticsearch.transport-port
2. elasticsearch.cluster-name
3. elasticsearch.cluster-address

All three of them are being set with environment variables under the tokens: ES_CLUSTER_PORT, ES_CLUSTER_NAME, and ES_CLUSTER_URL.

We will need to add these new envrionment variables to our runtime configuration in order to run our project. In IntelliJ click the dropdown box for your runtime configurations. Edit, and add the new environment variables.

* ES_CLUSTER_NAME = docker-cluster
* ES_CLUSTER_PORT = 9300
* ES_CLUSTER_URL = localhost

.. note::
  When we move this project to the cloud we are going to update these values to match the values associated with the EC2 where elasticsearch lives.

5) Run our application
______________________

Time to run our application locally to make sure everything was configured correctly.

Our map should load from OSM, however our features won't load because we are using a brand new version of Elasticsearch and it hasn't been seeded yet.

We will need to seed Elasticsearch from postgis before any of our features will be displayed.

While our web application is running ::

  $ curl -XPOST http://localhost:8080/api/_cluster/reindex

This is how we have been seeding Elasticsearch with this project. Our EsController file dictates that when a POST request is made to /api/_cluster/reindex anything inside of elasticsearch is deleted, and it is re-created from the reporst in the database.

.. note::

  If you have issues running your application locally, make sure elasticsearch isn't running on brew, your docker container is running, you have brought in all week6-starter changes, and you have created new elasticsearch environment variables. If it still isn't working delete, and re-create a docker container with elasticsearch in it.


6) Run our tests
________________

Now that we have our new version of elasticsearch running in docker, and it is populated we need to re-run our tests to ensure our changes didn't break anything unexpectedly.

Fix any tests that fail.

Once your tests are passing, you have completed this milestone and are ready to take your project to AWS!


Milestone 2 - Setup and configure VPC on AWS
--------------------------------------------

Before we can deploy the local version of our application to the cloud we will need to create, and configure a VPC, an RDS, an Ubuntu EC2, and a CentOS EC2.

Although we can create most of these things in any order, you must create your VPC first, and it's easiest to create your CentOS last because it needs some information about the RDS, and the Ubuntu EC2.

In the cloud folder of the week6-starter repo you will find some very handy scripts that will help you setup and configure your VPC, and EC2s.

1. Create a VPC
2. Create an RDS
3. Create an Ubuntu EC2
4. Send your jar to an S3 bucket
5. Create a CentOS EC2

1) Create a VPC
_______________

During our instruction week we `created a VPC <../../studios/aws-rds-vpc/>`_ for our airwaze project. This week we will need to create a new VPC for our zika project.

Review the instructions for last week, and use the `new configuration file <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week6-starter/cloud/zika_cloudformation.json>`_ found on GitLab.

This configuration creates:

1. Two public subnets with an internet gateway (each in their own availability zone).
2. Two private subnets (each in their own availability zone).
3. One security group for web servers (ports 80 and 22 open). ``WebAppSecurityGroup``
4. One security group for databases (port 5432 open). ``DatabaseSecurityGroup``
5. One security group for load balancers (port 80 open). ``ELBSecurityGroup``

2) Create an RDS
________________

We need a Postgres database to store our information on the cloud. We will need to setup a new RDS using our new VPC.

Read over the `steps we followed last instruction week <../../studios/aws-rds-vpc/>`_ to remind yourself how to create an RDS.

Don't forget to write down the RDS endpoint, the DB name, the RDS master user, and the RDS master user password. You will need these to configure your zika web app. They will eventually go in the zika.config file on the EC2 that serves the zika web app.


3) Create an Ubuntu EC2
_______________________

In the cloud you will be running Elasticsearch on it's own EC2 instance. So we will need to create a new Ubuntu EC2 using our new VPC. You should use the `elastic_userdata.sh <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week6-starter/cloud/elastic_userdata.sh>`_ found on GitLab.

* You will need to spin up a ``Ubuntu`` ``t2.medium`` EC2 instance to serve Elasticsearch (Elasticsearch requires lots of memory)
* Use the ``startup_Elasticsearch.sh`` `script <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week6-starter/cloud/elastic_userdata.sh>`_ in the week6-starter project to configure a ``t2.medium`` machine.
* You can check on the status of Elasticsearch by sshing into the server and running ``$ journalctl -f -u Elasticsearch``
* If you get an "Out of Memory Exception", be sure to increase the heap size by setting ``Xms3g`` and ``Xmx4g`` in the ``/etc/Elasticsearch/jvm.options`` file.

Don't forget to write down the private IP address of this EC2 instance. This will be used in the zika.config file so that our zika web app can talk to the elasticsearch stored on this EC2 instance.

After setting this EC2 instance up, it would be a good idea to ssh into this instance, and then make a curl request to localhost:9200 which should return a response of information about this elasticsearch cluster, the name should be docker-cluster, and the version should be 5.6.0. If you don't see this information look back at the elastic_userdata.sh script, and verify it ran correctly.

4) Send your app.jar file to an S3 bucket
_________________________________________

In preparation to create the EC2 that will host our web app we need to send the .jar file to an S3 bucket.

The centos_userscript we will run to setup our CentOS EC2 pulls from an S3 bucket. You will need to create a new S3 bucket, or use the one from last week.

Refamilarize with `S3 buckets <../../studios/aws-auto-scaling/>`_, and then send your .jar file to the S3 bucket you will use for this project.

After completing this step, make sure you have the endpoint of the S3 bucket that hosts your app.jar file, and that your app.jar file can be read.

5) Create a centos ec2
______________________

Our zika web application will live on a CentOS EC2 instance in our VPC. We will configure our CentOS EC2 to run the `centos_userdata.sh <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week6-starter/cloud/centos_userdata.sh>`_ script found on GitLab.

This script does a few things on startup:

1. Install java
2. Install aws
3. Open port 80
4. Create zika user, zika user folders
5. Get app.jar from YOUR-S3-BUCKET (you will have to change this line to point to your S3 bucket)
6. Grant privileges to the zika user folders and the app.jar file, to the zika user
7. Creates the zika.config file that contains all of our environment variables (You will have to change many of these to match your RDS, and Ubuntu EC2)
8. Creates our systemd zika.service
9. Enables, and starts our zika.service

You can include this script to run on EC2 startup when you are creating your instance by clicking advanced configuration, and pasting in the contents of this script.

It should be noted that your environment variables will be different than the example environment variables, and everyone else's environment variables.

* APP_DB_HOST=YOUR-RDS-ENDPOINT
* APP_DB_NAME=YOUR-RDS-DB-NAME
* APP_DB_USER=YOUR-RDS-MASTER-USER
* APP_DB_PASS=YOUR-RDS-MASTER-USER-PASSWORD
* ES_CLUSTER_URL=YOUR-UBUNTU-EC2-PRIVATE-IP-ADDRESS

.. note:

  If you forget to include the script in the advanced configurations, don't worry you can always run this as a script from the terminal of your CentOS EC2.

`CentOS`` is a free, enterprise class, Linux distribution based on Red Hat Enterprise Linux. Most of the commands will be the same as Ubuntu, except the package manager will use ``yum install`` instead of ``apt-get install``. CentOS comes with less software installed than Ubuntu. For example ``telnet`` has to be installed via ``sudo yum install telnet``. `Info on Image of CentOS we will use <https://wiki.centos.org/Cloud/AWS>`_

.. note::

  To ``ssh`` into a CentOS instance, you will need to use the username ``centos``.

How to manually create an AWS EC2 instance using CentOS

* Go to Oregon Region
* Click **Launch Instance** in the EC2 Dashboard
* Click **My AMIs**
* Search for **centos**
* Click **CentOS Image**

CentOS Image

.. image:: /_static/images/centos-image.png

.. note::

  After all of this, it is a great idea to ssh into your CentOS EC2, to make sure the configuration completed the steps listed above. If so run `journalctl -u zika` to see if the zika app started without any errors. At this point if you have errors, make sure your zika.config file matches the RDS, and EC2 information. Use telnet, and ssh to make sure the CentOS, Ubuntu, and RDS servers can all talk to each other.

Milestone 3 - Access your application via a live URL
----------------------------------------------------

1. View base map at live url
2. Add extensions to RDS (Postgres)
3. Populate RDS (Postgres)
4. Seed elasticsearch
5. View fully functioning app

1) View base map at live url
____________________________

Now that we setup our VPC, and spun up an RDS, CentOS EC2, and Ubuntu EC2. Our application should run.

Go to `http://your-centos-ec2-public-dns.com` to see your application in action, via a live URL.

We have yet to populate our database, and elasticsearch, so no features should be displayed. However, the base map from OSM should load properly. Just running your application shows us that we configured our VPC correctly, and that the 3 pieces can all talk to each other.

2) Add extensions to RDS
________________________

We created an RDS and put Postgres on it, however we did not add the extensions we need to create the tables that store GEOINT.

We will need to login to our RDS from our CentOS application to create the extensions, and copy over data from our CSV files.

From the terminal of your CentOS machine::
  
  $ sudo yum install postgresql

This install postgresql cli onto your centos machine.

From the terminal of your CentOS machine::
  
  $ psql -h YOUR-RDS-ENDPOINT -p 5432 -U YOUR-RDS-MASTER-USER -d YOUR-RDS-DB-NAME

This will drop us into the psql cli on our RDS. From here we can run any sql command we normally would, including our add extension commands.

From the psqlcli inside your RDS::

  $ CREATE EXTENSION postgis;
  $ CREATE EXTENSION postgis_topology;
  $ CREATE EXTENSION fuzzystrmatch;
  $ CREATE EXTENSION postgis_tiger_geocoder;
  $ \q

These are the extensions we needed to add to turn our postgres database to postgis which allows for storing location data. \q simply drops you out of your RDS and back into your CentOS machine.

Hibernate tries to create our report, and location tables for us, but before we added the postgis extensions, it was unable to do so properly. We will need to stop and re-start our zika.service so that it can create the tables correctly.

From the terminal of your CentOS machine::

  $ sudo systemctl stop zika
  $ sudo systemctl start zika
  $ sudo journalctl -f -u zika

These commands stop and restart zika, and print out the journal entries of zika in real time. After the application has started again it should have created the location, and report tables for you. You should verify this by logging back into your RDS.

From the terminal of your CentOS machine::

  $ psql -h YOUR-RDS-ENDPOINT -p 5432 -U YOUR-RDS-MASTER-USER -d YOUR-RDS-DB-NAME

From the psqlcli inside your RDS::

  $ \dt

You should see a report table, and a location table. They are still empty, but they should exist.

3) Populate RDS
_______________

Now that both of the tables exist, we need to populate them by copying over the information from our CSV files. If you haven't used scp to copy your .csv files from your local machine to your CentOS machine do that now. We will need both location.csv, and all_reports.csv.

From the terminal of your CentOS machine::

  $ psql -h YOUR-RDS-ENDPOINT -p 5432 -U YOUR-RDS-MASTER-USER -d YOUR-RDS-DB-NAME -c "\copy location(ID_0,ISO,NAME_0,ID_1,NAME_1,HASC_1,CCN_1,CCA_1,TYPE_1,ENGTYPE_1,NL_NAME_1,VARNAME_1,geom) from STDIN WITH DELIMITER E'\t' CSV" < locations.csv
  $ psql -h YOUR-RDS-ENDPOINT -p 5432 -U YOUR-RDS-MASTER-USER -d YOUR-RDS-DB-NAME -c "\copy report(report_date, location, location_type, data_field, data_field_code, time_period, time_period_type, value, unit) from STDIN WITH DELIMITER ',' CSV HEADER" < all_reports.csv

These two commands copy our all_reports.csv, and locations.csv file into the database on our RDS. We should make sure it copied everything correctly, let's drop back into our RDS psqlcli.

From the psqlcli inside your RDS::

  $ SELECT COUNT(*) FROM reports;
  $ SELECT COUNT(*) FROM locations;


You should see a total of 250 locations, and over 240000 reports. If you don't see this number of reports, drop the report, and location tables, stop and start your zika service, run the copy commands again and check again.

We have one final thing we need to do for our database to work correctly. We need to unaccent two of the columns on our location table. Our web app is expecting location names to not contain hyphens, but our tables currently have locations with hyphens. We need to unaccent them!

From the psqlcli inside your RDS (after your tables have been populated)::

  $ CREATE EXTENSION unaccent;
  $ UPDATE location SET name_0_normalized = unaccent(name_0);
  $ UPDATE location SET name_1_normalized = unaccent(name_1);

That's it! We now have an RDS with postgres, postgis, data from csvs, and it has been properly unaccented. We can move to the next step.

4) Seed elasticsearch
_____________________

Now that our database has been populated we can use it to seed our database. We have been using curl to send a POST request to the endpoint '/api/_cluster/reindex' to seed our database. We still need to do this, but from the command line of our CentOS machine, while the app is running.

If your zika aspp isn't running, use systemctl, and journalctl to get it going and troubleshoot any issues.

From the terminal of your CentOS machine::

  $ curl -XPOST localhost:8080/api/_cluster/reindex

This command should take a few seconds. It is deleting anything in our elasticsearch cluster, and then recreating it from the reports in the RDS. 

.. note::
  
  If it fails to run and you see an error like: 'java heap size' our CentOS instance does not have enough memory to process this command. You will need to increase the instance.type of this EC2, or change the code in EsUtil.

After it has run successfully you should check it out from your CentOS machine.

From the terminal of your CentOS machine::

  $ curl http://YOUR-UBUNTU-PRIVATE-IP-ADDRESS:9200/report/_count

This command will return a count of the number of documents in the report index on the Elasticsearch server.

If you don't see 240000+ records, or a java-heap error make sure your CentOS, and Ubuntu machines can talk to each other and have been configured properly.

5) View fully functioning app
_____________________________

Now that our RDS with Postgres, and our Ubuntu with Elasticsearch have data, our web app should display features correctly. Let's check it out.

In the browser of your local machine go to `http://YOUR-CENTOS-PUBLIC-DNS.com/`

You should see your map with features, and the full functionality of your local project.

.. hint::

  If you make changes to your local project, you will need to rebuild your .jar file, and send it to your CentOS machine. You may have to remove the old app.jar file, restart the systemctl service, and generally troubleshoot as issues arise. If you change the .jar file on your CentOS machine you should also update the app.jar file in your S3 bucket.

Debrief
=======

We did a lot this week!

1. We changed our local development environment to more closely match the AWS environment.
2. We created a new VPC with an RDS, CentOS EC2, Ubuntu EC2
3. We worked with an S3 bucket to host our app.jar file
4. We manually populated the report, and location table in RDS Postgres database
5. We used our RDS Postgres database to seed elasticsearch that lives on it's own EC2
6. We configured a CentOS EC2 to run our web application, and communicate with Elasticsearch (Ubuntu EC2) and Postgis (RDS Postgres)

Working with AWS, and cloud computing in general can be frustrating. We have so many technologies all working together, and if one thing is misconfigured, or forgotten it can bring the whole application crashing down. 

You should be proud of what you have accomplised this week!

Bonus Missions
==============

After you completed the three milestones above, and you are happy with your project. You should add a load balancer to your VPC.

Read over `the instructions <../../studios/aws-auto-scaling/>`_ for how we did this last week, and then give it a shot!

You can think implement Jenkins to automatically push an app.jar file to your S3 when all of the tests pass.

You can use ESLint to ensure your JavaScript is up to par.