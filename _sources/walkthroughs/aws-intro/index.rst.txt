:orphan:

.. _aws-intro-walkthrough:

=========================
Walkthrough: Intro to AWS
=========================

We will walkthrough getting signed into AWS console and then have a look around

Log in to AWS Console
=======================
* You should have received a aws console login link in your email
* Please login to the AWS Console using that link

Look Around AWS Console
=======================

Top Menu
--------

* Services Menu for finding available AWS services (top left)
* Has drop down menu for your account (top right)
* Has drop down menu for the current Region (top right)
* Support Menu for getting help or finding docs (top right)


Regions
-------

Regions are seperate geographic locations where you can deploy AWS services. **Everything in the AWS UI is based off of
Region**, so if you don't see a instance you created you are probably in the wrong Region. More about Regions and Availability Zones https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html

AWS Regions
-----------

  .. image:: /_static/images/regions.png

Services Search Bar and Menu
----------------------------

This is where AWS can get a little overwhelming because there are so many services available. We are only to look at a
few in the class.  On your own time, please read about all the new terms/tools you see listed here. You may want to use
them some day. Our focus today is ``EC2``.

What is EC2?
------------

* EC2 = Elastic Cloud Compute

  * **NOTE:** This use of Elastic != ElasticSearch
  * EC2 and ElasticSearch are two different things
  * We will eventually install and run ElasticSearch on an EC2 server

* EC2 is a virtual server you can configure, run, login to, restart, shut down, and destroy

  * You can install and run anything you want on the servers you create
  * The only limitation is the amount of money you want to pay Amazon for running the servers $$$

* EC2 servers run in Amazon's AWS cloud

AWS Services
------------

  .. image:: /_static/images/services.png

Secure Your AWS Account
=======================

We want to make sure our AWS accounts are secure. To do that we are going to setup Two Factor Authentication. AWS is a very powerful tool and we want to make sure our accounts are only used by us.

Add Two Factor Authentication
-----------------------------

1. Go to **IAM** via **Services**
2. Click **Users**
3. Click your username
4. Click **Security credentials** tab
5. Click the pencil for **Assigned MFA device**
6. Download an Authenticator such as Google Authenticator on your phone
7. Open the Authenticator app on your phone
8. Now connect your AWS Account to the Authenticator app by entering informatin provided by AWS

   * You can use the provided barcode
   * Or you can enter the provided informatin

9. Next enter a key from your Auth app into AWS and then wait for it to expire and enter the next key that appears
10. Next log out and see if works. Fingers crossed. Don't worry your instructor can unlock you account if needed.
