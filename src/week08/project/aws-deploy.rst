.. _week8_project_deploy:

=======================================
Week 8 - Project Week: Deploying to AWS
=======================================

``cloud/geoserver_userdata.sh`` is provided to create a GeoServer instance in AWS

Before getting started, **be sure that your Boundless virtual machine is not running**. We will be using the same port as the VM with one of our Docker containers, so if it is running there will be conflicts. It is not enough to pause the VM; you must shut it down completely or select *Save State*.

Docker Commands
---------------

* ``docker ps`` see list of **running** containers
* ``docker ps -a`` see lisf of all containers, including ones that failed or were stopped
* ``docker start <container-name or id>`` starts the container
* ``docker stop <container-name or id>`` stops the container
* ``docker restart <container-name or id>`` restarts the container
* ``docker rm <container-name or id>`` removes the container
* ``docker images`` shows list of images that you have downloaded. containers are created from images
* ``docker image rm <image-name or id>`` removes an image
* For more info and more commands please see `the Docker CLI docs <https://docs.docker.com/engine/reference/commandline/docker/>`_

.. warning::

  If your containers start crashing with exit code 137, it's because they are out of memory. You need to incrase the memory given to Docker by going to Peferences, Advanced. See screen shot below.

.. figure:: /_static/images/docker-memory.png

   Docker Memory Configuration

Create PostGIS Container
------------------------

First create a file ``env.list`` in the root of your ``zika-cdc-dashboard`` project folder, with the same contents as `our envlist file <https://gist.github.com/chrisbay/d74442a8e8707111472a742832d76796>`_.

Next run this command to create a PostGIS container referencing ``env.list`` from above. We have to use a Docker instance of PostGIS so that our GeoServer Docker instance and our local web application can both connect to PostGIS. ::

  $ docker run --name "PostGIS" -p 5432:5432 -d -t --env-file ./env.list kartoza/PostGIS:9.4-2.1

.. warning::

  In ``env.list`` you'll see that the ``POSTGRES_DBNAME`` environment variable is set to ``zika``. This variable is supposed to set the name of our PostGIS-enabled database within the container to be ``zika``. However, a bug in the Dockerfile for this image ignores the name, creating a database named ``gis``.

Verify that the container is running by running ``docker ps``.

Create GeoServer Container
--------------------------

We are going to link the PostGIS and GeoServer containers. That tells docker that these containers need to be able to communicate. ::

  $ docker run --name "geoserver" --link PostGIS:PostGIS -p 8080:8080 -d -t kartoza/geoserver

.. warning::

  If the ``PostGIS`` docker image is not running when starting the geoserver, the link will fail.

When its container is running, you can access this GeoServer instance the same way in which you previously accessed GeoServer locally when running the Boundless virtual machine. It will be running on port 8080 (try ``http://localhost:8080/geoserver``) with credientials **admin / geoserver**.


Create Elasticsearch Container
------------------------------

::

  $ docker run --name "es" -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:5.6.0

.. warning::

  If Docker has no more than 2G of memory allocated for container use, you may have issues with the ``elasticsearch`` container crashing due to lack of memory. If this happens, increase memorgy to at least 3G by going to *Docker > Preferences > Advanced*.

Enable CORS in GeoServer
------------------------

You'll be making requests to the GeoServer container from a port other than the one on which GeoServer is running, which means CORS will come into play. Let's enable cross-origin requests within GeoServer.

.. tip:: You may want to wait until you actually see a `CORS <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS>`_ error in your browser's JavaScript console before performing these steps.

Open a shell within the Docker container and install a text editor (you can also install ``nano`` instead of ``vim`` if you want):::

  $ docker exec -it geoserver bash
  root@2992f761f41e:/usr/local/tomcat# apt-get update
  root@2992f761f41e:/usr/local/tomcat# apt-get install vim

Open the GeoServer ``web.xml`` for editing:::

  root@2992f761f41e:# vi /usr/local/tomcat/conf/web.xml

Add the following XML just within the opening ``<web-app>`` tag:

.. code-block:: xml

  <filter>
    <filter-name>CorsFilter</filter-name>
    <filter-class>org.apache.catalina.filters.CorsFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>CorsFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

Save the file and exit. Then exit the docker container shell. ::

  root@2992f761f41e:# exit

Stop and start the ``geoserver`` container: ::

  $ docker stop geoserver
  $ docker start geoserver

Now ``XHR``requests from your local zika app running on ``http://localhost:9090`` will be accepted by our GeoServer instance. If you don't set that up, you will see ``CORS`` errors in the js console.

Populate Container PostGIS Database
-----------------------------------

We need to load the report and location data into the ``PostGIS`` docker container.  We will copy over the ``.csv`` files to the container and execute psql copy commands.

* First, let's change the paths referenced in the ``/src/main/resources/data.sql`` file to be ``'/tmp/locations.csv'`` and ``'/tmp/all_reports.csv'``
* Then copy the files to the ``PostGIS`` contianer:

::

  $ docker cp locations.csv PostGIS:/tmp
  $ docker cp all_reports.csv PostGIS:/tmp


Verify that the files made it: ::

  $ docker exec -it PostGIS ls -l /tmp

Remember that ``data.sql`` makes use of the ``unaccent`` function, which is part of the ``unaccent`` Postgres extension. While our Docker image came with the PostGIS extension installed, the ``unaccent`` extention is **not** present. Let's fix that.

Also ``data.sql`` will not actually be executed by Spring Data. If you rename it to ``import.sql`` and edit property ``spring.jpa.hibernate.ddl-auto`` in ``application.properties``. If ``spring.jpa.hibernate.ddl-auto`` is either ``create`` or ``create-drop``, then ``import.sql`` will run. After you database has been initialized you can change the value to ``validate``. More here on `Spring Data - Database Initialization <https://docs.spring.io/spring-boot/docs/current/reference/html/howto-database-initialization.html>`_

.. warning::

  Stop all instances of Postgres on your local machine. Stop the Postgress App in the top bar and stop the service being managed by ``brew``. The only Postgres we want running is the one inside of the Docker container. If you get an error below that the ``gis`` database doesn't exist, then you are connected to the wrong Postgres instance.

Fire up ``psql``, note the password for ``zika_app_user`` is ``somethingsensible``: ::

  $ psql -h localhost -p 5432 -U zika_app_user -d gis

And then install the extension: ::

  # create extension unaccent;

Exit ``psql``.

Deploying to AWS
================

To deploy GeoServer on AWS you will be using a ``t2.small`` CentOS machine.

Paste the contents of shell script `geoserver_userdata.sh <https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week8-starter/cloud/geoserver_userdata.sh>`_ into the "Advanced Details" details section of "Configure Instance" to create the instace.  The script installs Apache Tomcat, downloads the Boundless Suite WAR, and deploys the geoserver WAR the Apache Tomcat server.  The deployed geoserver can be reached on ``http://{your IP}:8080/geoserver``.

.. tip::

  Remember the default username for Geoserver is ``admin`` and the default password is ``geoserver``.

Bonus Mission
-------------

When you complete all of these instructions, check out the `ElasticGeo Plugin <https://github.com/ngageoint/elasticgeo>`_. It is an Elasticsearch plugin that allows you to integrate Elasticsearch into Geoserver. The great thing is that you can do Elasticsearch queries directly through Geoserver via WFS calls. Here are the setup instructions and instructions on how to make the calls: `ElasticGeo Instructions <https://github.com/ngageoint/elasticgeo/blob/master/gs-web-elasticsearch/doc/index.rst>`_
