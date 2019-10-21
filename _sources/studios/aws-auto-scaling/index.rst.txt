:orphan:

.. _studio-aws-auto-scaling:

======================================
Scaling horizontally AutoScaling Group
======================================

Overview
========

The ability to scale horizontally is very important in building Cloud Native applications.  In this studio, you will be extending your `Airwaze App <https://gitlab.com/LaunchCodeTraining/airwaze-studio>`_ to scale horizontally as traffic on the server increases.

New Topics
----------
* Cloud Formation Script for VPC
* S3 Bucket
* AWS CLI
* Auto Scaling

Setup
=====

You can do this studio in any **US Region** that has not reached it's VPC limit. N.Virginia and Oregon have the highest VPC limit.

Create a KeyPair for the Region (if in new Region)
--------------------------------------------------

**IF** you are in a new Region, you will need to create a new KeyPair. 

1. Make sure you are in the new region
2. Go to EC2 in the services menu
3. Click **Key Pairs** in the left menu in the **NETWORK & SECURITY** section
4. Enter a name like ``yourname-region-name-key``
5. Download key
6. Copy the key to your ``~/.ssh`` folder
7. Make it so that only the owner can read and write to the file ``$chmod 400 yourname-useast-key.pem``

1) AWS CLI
==========

We will be using the `AWS CLI tool <https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html>`_ for some parts of the studio.  The AWS CLI tool allows you to create and change infrastructure on the cloud via the command line.

Install AWS CLI
---------------

To install the AWS CLI tool run the following commands in your terminal:

::

  (on local computer)
  $ brew install awscli
  $ aws --version

AWS CLI Credentials
-------------------

Create AWS CLI credentials

1. Go to **Services > IAM > Users**
2. Click on **yourself** in the list
3. Click **Security credentails** tab

Screenshot of IAM credentails

  .. image:: /_static/images/day3/iam_get_credentials.png

4. Click **Create Access Key**
5. Copy or write down the **Secret Key**
  
   * **WARNING**: You will not be able to see the secret code after closing the new key window
   * If you do close the window before copying the key, you can remove the key and create a new one

Screenshot of Create Access Key

  .. image:: /_static/images/aws-cli-key.png  


.. note::

  It is very important that you keep the ``AWS Secret Access Key`` **private**.  Keep it secret, Keep it safe!
  Access to that key allows anyone to programmatically create infrastructure(servers, rds, etc) on the AWS account.

Enter Your Credentials into AWS CLI
-----------------------------------

Next run the below command and configure your the AWS CLI tool.  Use the "Default region name" or ``us-east-1``:

::

  (on local computer)
  $ aws configure
  AWS Access Key ID [None]: AK-------------------
  AWS Secret Access Key [None]: r4------------------
  Default region name [None]: (just hit enter)
  Default output format [None]: (just hit enter)

Run AWS CLI Commands
----------------------

You should now be able to run commands against AWS.  For example, you should now be able to list all of the buckets in S3:::

  $ aws s3 ls


Take a look around by looking at the help pages for a couple of commands:::

  $ aws help
  $ aws s3 help
  $ aws s3 sync help


The ``aws help`` command is a quick alternative to looking up information about the tool on line.

2) Configure your VPC via CloudFormation
========================================

You are going to use `Amazon CloudFormation <https://aws.amazon.com/cloudformation/>`_ to create your VPC.  CloudFormation can create infrastructure on AWS based on a JSON template.  CloudFormation allows you to create consistent, reproducible AWS environments.

You'll be using a CloudFormation template that adds:

* 4 Subnets
* 3 Security Groups
* 1 RDS

Finally AWS CloudFormation will pull the template from an S3 bucket.

Download and Review the CloudFormation Script
---------------------------------------------

* Take a look at the template by downloading it with the ``aws-cli`` tool (command shown below)
* Then open ``airwaze_cloudformation.json`` in your favorite editor
* You should recognize the names and properties listed from previous studios

  * The only new thing is seeing them in this format.
  
::

  $ mkdir ~/s3-sync/cloud
  $ aws s3 sync s3://launchcode-gisdevops-cloudformation ~/s3-sync/cloud
  $ cd ~/s3-sync/cloud
  (then open the airwaze_cloudformation.json file)


Create VPC with CloudFormation Script
-------------------------------------

1. Go to services menu
2. Enter "CloudFormation" into the search bar
3. Click on **Cloud Formation** search result 
4. Click orange **Create Stack** button
5. Choose **Specify an Amazon S3 template URL** and paste in https://s3.amazonaws.com/launchcode-gisdevops-cloudformation/airwaze_cloudformation.json
6. Click **Next**

Screenshot of CloudFormation Screen 1

  .. image:: /_static/images/day3/stack_screen_1.png

Next we need to give your stack a name and pass along a few parameters to customize the VPC.

7. Fill in **Stack Name** with "airwaze-{your name}".
8. Fill in **DatabasePassword** with "verysecurepassword" (not this exact password, something you want).
9. For **KeyName** select your Key Pair(.pem file) for this Region

Screenshot of Stack parameters

  .. image:: /_static/images/day3/stack-parameters2.png

* Click Next on the "Options Screen"
* Click Create on the "Review Screen"

It will take CloudFormations about 15 minutes to create and run your VPC.  The "Events" tab will give you continuous updates on the progress of the job.
Be sure to note the name and VPC ID of the VPC that is created.

3) Configure Buckets
====================

Since you will be scaling machines horizontally, you **WON'T** be able to manually ``scp`` a jar to each machine.  Instead, the machines will reach out and grab a copy of the jar when they start.  The servers will download a copy of your application jar from S3.

First create a new bucket in S3.  Remember **EVERY** bucket name for S3 in the whole wide world has to be unique.  Use the pattern below to get a unique name.::

  $ aws s3 mb s3://launchcode-gisdevops-c1-yourname/


Run ``aws s3 ls`` to make sure that the bucket was created properly.

Put your .jar in the Bucket
----------------------------

Locate a ``.jar`` for Airwaze that you deployed for Day 2 Studio. Rename it to ``app.jar`` and upload the jar to S3 using the following command:::

  $ aws s3 cp build/libs/app.jar s3://launchcode-gisdevops-c1-yourname/
  $ aws s3 ls s3://launchcode-gisdevops-c1-yourname/ # check to make sure it uploaded

4) Create an EC2 to Populate the Database
=========================================

You are going to create an EC2 do some initial database setup. This EC2 will not be used for anything else. Please name it ``your-name-day3-db-setup``

* Create an EC2 instance of the same type as previous days
  
  * Select the VPC that was just crated by the CloudFormation
  * Select ``{yourname}-airwaze-SubnetWebAppPublic`` as the subnet

.. image:: /_static/images/ec2-vpc-subnet2.png


* Once the server is up, SSH into the server and run the following commands:

::

  (on remote server)
  ubuntu$ sudo apt-get update
  ubuntu$ sudo apt-get install postgresql
  ubuntu$ psql -h airwaze-example.cew68jaqkoek.us-east-1.rds.amazonaws.com -p 5432 -U masterUser airwaze

::
   
  (paste this sql into psql shell)
  CREATE USER airwaze_user WITH PASSWORD 'verysecurepassword';
  CREATE EXTENSION postgis;
  CREATE EXTENSION postgis_topology;
  CREATE EXTENSION fuzzystrmatch;
  CREATE EXTENSION postgis_tiger_geocoder;
  CREATE TABLE airport
  (
  id serial primary key,
  airport_id integer,
  airport_lat_long geometry,
  altitude integer,
  city character varying(255),
  country character varying(255),
  faa_code character varying(255),
  icao character varying(255),
  name character varying(255),
  time_zone character varying(255)
  );
  CREATE TABLE route
  (
  id serial primary key,
  airline character varying(255),
  airline_id integer,
  dst character varying(255),
  dst_id integer,
  route_geom geometry,
  src character varying(255),
  src_id integer
  );
  ALTER TABLE airport OWNER to airwaze_user;
  ALTER TABLE route OWNER to airwaze_user;


Also, send up the ``routes.csv`` file and the ``Airports.csv`` file and get those in the database.::

  (on local computer)
  $ scp -i ~/.ssh/mikes-keys.pem routes.csv  ubuntu@35.170.78.180:/home/ubuntu
  $ scp -i ~/.ssh/mikes-keys.pem Airports.csv  ubuntu@35.170.78.180:/home/ubuntu

Then after the csv files have been copied to the server you can populate the database by running these commands.

::

  (remote server)
  ubuntu$ psql -h airwaze-example.cew68jaqkoek.us-east-1.rds.amazonaws.com -d airwaze -U airwaze_user -c "\copy route(src, src_id, dst, dst_id, airline, route_geom) from STDIN DELIMITER ',' CSV HEADER" < /home/ubuntu/routes.csv
  ubuntu$ psql -h airwaze-example.cew68jaqkoek.us-east-1.rds.amazonaws.com -d airwaze -U airwaze_user -c "\copy airport(airport_id, name, city, country, faa_code, icao, altitude, time_zone, airport_lat_long) from STDIN DELIMITER ',' CSV HEADER" < /home/ubuntu/Airports.csv


5) Create the Launch Configuration
==================================

You now have all of the pieces set up to begin Auto Scaling EC2 machines.

* Navigate to `AutoScaling Page <https://console.aws.amazon.com/ec2/autoscaling/home>`_ on the sidebar of EC2
* Click **Create Auto Scaling Group**.

Screenshot of AutoScale Start

  .. image:: /_static/images/day3/create_auto_scaling_group.png

A LaunchConfiguration is essentially creating a template for all of the EC2 instances that will be created automatically via Auto Scale.

* You are going to create a new Launch Configuration.

Screenshot of AutoScale Step 1

  .. image:: /_static/images/day3/auto_scale_step_1.png

The Launch Configuration is going to be very similar to setting up a normal EC2 instance.

* Choose the Ubuntu distribution on the AMI screen.

Screenshot of Auto Scale AMI

  .. image:: /_static/images/day3/auto_scale_ami.png

* Choose the micro instance.

Screenshot of Auto Scale instance size

  .. image:: /_static/images/day3/auto_scale_instance_size.png

There are several important configurations that have to be made on the **Configure Details** screen.

The most important configuration is the User data in Advanced Details.  The **User data** is the script that runs as the server starts up.  This script creates the proper directories, configures systemd, and launches the app. Additionally, the app pulls down a copy of the jar file from S3.

There are two pieces of data to change in the **User data** script:

* Copy the **User Data script** that is provided below and paste it into an editor
* Set ``APP_DB_HOST`` to the endpoint of your RDS database.
* Change the ``aws s3 c s3://launchcode-gisdevops-c1-yourbucket/app.jar /opt/airwaze/app.jar`` command to point to the bucket that you created earlier in the studio.
* Paste your updated script in the "User data" field.
* Set "IAM role" to "EC2_to_S3_readonly". When the machine is starting, the startup script will need to reach out to S3.  The "IAM role" gives the startup script the proper credentials to be authenticated to access S3.
* Set the name of the configuration to ``airwaze-{your name}-config``.
* Change the "IP Address Type" to be ``Assign a public IP address to every instance``.

Screenshot of Auto Scale configuration

  .. image:: /_static/images/day3/auto_scale_config.png

* Click "Next: Configure Security Group"
* On the Security Group screen, choose the ``WebAppSecurityGroup`` from your VPC.  The key is that you want to have ports 22 and 8080 open on the machines that you are running.
* Click "Review"
* Click "Create Launch configuration"

User Data Script (remember to change certain parts)
---------------------------------------------------
::

  #!/bin/bash
  # Install Java
  apt-get update -y && apt-get install -y openjdk-8-jdk awscli

  # Create airwaze user
  useradd -M airwaze
  mkdir /opt/airwaze
  mkdir /etc/opt/airwaze
  aws s3 cp s3://launchcode-gisdevops-c1-traineemike/app.jar /opt/airwaze/app.jar
  chown -R airwaze:airwaze /opt/airwaze /etc/opt/airwaze
  chmod 777 /opt/airwaze

  # Write Airwaze config file
  cat << EOF > /etc/opt/airwaze/airwaze.config
  APP_DB_HOST=airwaze-example.cew68jaqkoek.us-east-1.rds.amazonaws.com
  APP_DB_PORT=5432
  APP_DB_NAME=airwaze
  APP_DB_USER=airwaze_user
  APP_DB_PASS=verysecurepassword
  EOF

  # Write systemd unit file
  cat << EOF > /etc/systemd/system/airwaze.service
  [Unit]
  Description=Airwaze Studio
  After=syslog.target

  [Service]
  User=airwaze
  EnvironmentFile=/etc/opt/airwaze/airwaze.config
  ExecStart=/usr/bin/java -jar /opt/airwaze/app.jar SuccessExitStatus=143
  Restart=always

  [Install]
  WantedBy=multi-user.target
  EOF

  systemctl enable airwaze.service
  systemctl start airwaze.service

Screenshot of Auto Scale security groups

  .. image:: /_static/images/day3/auto_scale_security_groupsv3.png

6) Create the Auto Scale Group
==============================

The Auto Scale Group is the piece of configuration responsible for how and when new machines are spun up (and spun down). Spun up = created and started. Spun down = stopped and possibly deleted.

The first step is configuring where the machines will be spun up.

* For "Group name", provide a name similiar to ``airwaze-{your name}`` (replace {your name} of course...)
* For "Network", choose your VPC.
* For "Subnet", choose the ``SubnetWebAppPublic``.
* Click "Next: Configure Scaling Policy"

Screenshot of Auto Scale configuration

  .. image:: /_static/images/day3/auto_scale_group_1.png

The next screen configures how an app scales up.

* Select ``Use scaling policies to adjust the capacity of this group``.
* Mark that the app can scale up to 5 machines.
* Change the name to ``Scale Fast!``.
* Set the "Target value" to 5.  "Target value" is the percentage of CPU that triggers another machine to be provisioned.
* Set the "Instances need" to 40 seconds.  Since Spring Boot packages the web server in the jar, your application doesn't need as much start time as other machines.
* Click "Next: Configure Notifications"

Screenshot of Auto Scale configuration

  .. image:: /_static/images/day3/auto_scale_group_config.png

* Click "Next: Configure Tags"
* Click "Review"
* Click "Create Auto Scaling Group"

This will create you Auto Scaling Group.  At first, the summary page will say 0 instances; it typically takes a couple of minutes to initialize.

Screenshot of Auto Scaling Group Dash

  .. image:: /_static/images/day3/auto_scaling_group_dash.png

The "Instances" tab will show you how many machines you currently have running in your Auto Scaling Group.

Screenshot of the Instances tab

  .. image:: /_static/images/day3/auto_scaling_instances_tab.png

Next you need to hook a load balancer up to your Auto Scaling Group.

* Go to *EC2 Dashboard > Auto Scaling Group*
* Find your Auto Scaling Group
* Click Edit

Screenshot of Target Groups Edit

  .. image:: /_static/images/day3/target_groups_click_edit.png

* Select ``WebAppTargets`` from the "Target Groups" drop down.

Screenshot of Target Groups select target

  .. image:: /_static/images/day3/target_groups_set_target.png

7) Placing Load on your App
===========================

Next, you want to test that your autoscaling is working properly.

Go to the public DNS of your ELB and hit refresh many times. You can even go to that address in multiple browsers at the same time. You are trying to send as many requests as possible to your ELB.


Beware of the Load Test Tool
----------------------------

You can **optionally** use a Node library called `loadtest <https://www.npmjs.com/package/loadtest>`_.
Loadtest measures the average latency time of a concurrent requests to a server.

.. note::

  Note: Tools like loadtest and Apache AB are like guns.  You don't point them live things unless you want to kill it.  It's fine to point a load test tool at your non production apps on AWS; in fact you need to make sure that it can handle load.  You would never want to point a load testing tool at a live site because:
  * It's your live site (your staging environment should be similar enough to production to replicate the error).
  * Your production site will be sitting behind one or more layers like a CDN.  Your load test is going to look a lot like a denial of service attack. Services like your CDN are designed to recognize and block attacks at the fringe of your network.  Running a load test against a live site is a good way to get your IP address blocked.

To install `loadtest <https://www.npmjs.com/package/loadtest>`_ install the following npm package globally (``-g``)::

  $ sudo npm install -g loadtest


Next, you can run a command to put load on the server. The following command runs 3 requests per second sending 3 concurrent results to the server at a time.::

  (this starts 3 threads and sends 3 requests a second, don't run this for very long!)
  loadtest -c 3 --rps 3 http://internal-airwa-WebAp-1CT34V4AX36U0-774969334.us-east-1.elb.amazonaws.com


