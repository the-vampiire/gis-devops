:orphan:

.. _docker-elasticsearch:

==================================
Docker Installation: Elasticsearch
==================================

Installation Steps: Mac
-----------------------

#. Create env.list
#. Create container
#. Remove env.list
#. CURL elasticsearch cluster

Create env.list
+++++++++++++++

Before we create our container we first need to create a temporary file that will configure the container's discovery.type, and cluster.name.

* Create a new file in your terminal ``$ nano env.list``
* Add ``discovery.type=single-node`` to env.list
* Add ``cluster.name=elasticsearch`` to env.list
* Save env.list

This file contains two environment variables and configures them within our Elasticsearch container. The file configures the discovery type, and cluster name of our elasticsearch container.

Create Container
++++++++++++++++

We can now create, and run our container with ``$ docker run --name "es" -p 9200:9200 -p 9300:9300 -t --env-file ./env.list -d elasticsearch:6.5.4``

* ``docker run`` runs a command in a container (in this case it creates the container since it doesn't already exist)
* ``--name`` allows us to name our container (in this case we are naming our container "es")
* ``-p`` allows us to *publish* a port (host_port:container_port in this case we are opening ports 9200 & 9300)
* ``-d`` starts our container in *detach* mode which means it will run in the background
* ``-t`` allows us to run a terminal command (in this case adding the contents of our env.list file)
* ``--env-file ./env.list`` the type of file, and the file from our computers
* ``elasticsearch:6.5.4`` the image of the container (in this case elasticsearch version 6.5.4)

This command creates a container named "es" with the elasticsearch 6.5.4 image that listens on port 9200 and 9300, is detached from this command, and we are running a command to add the contents of env.list to our new "es" container.

We have our container listen on both ports 9200 and 9300 because Elasticsearch typically communicates over port 9200, anytime we make a request using cURL, or Kibana it uses port 9200. However, when we communicate with Elasticsearch from inside our Spring applications we will be using port 9300 to make our requests. Our container needs to be listening on both ports.

If you run ``$ docker ps -a`` now you should see a new container named "es".

.. hint::
   
   You can pass in environment variables without using env.list. By using ``-e "discovery.type=single-node" -e "cluster.name=elasticsearch"`` you can set the same configuration variables we set with env.list and ``-t --env-file ./env.list``

Remove env.list
+++++++++++++++

We no longer need the temporary env.list we created. You may delete it: ``$ rm env.list``.

CURL Elasticsearch Cluster
++++++++++++++++++++++++++

Verify your Elasticseach cluster is running with ``$ docker ps -a``.

If the container exists, but it's status is anything except ``Up`` start the container with ``$ docker start es``.

We can communicate with Elasticsearch in various ways throughout this class: HTTP, Spring Data, and Kibana. In this article we will use HTTP.

We can make HTTP requests using the cURL command line tool. Open a terminal and type: ``$ curl 127.0.0.1:9200``. Making a request to the root of elasticsearch provides you with information about this elasticsearch instance: name, cluster_name, cluster_uuid, version etc. This is an indicator that our container was installed, and configured correctly, and that we can communicate with the container.

Finally let's check the health of our cluster: ``$ curl 127.0.0.1:9200/_cluster/health``. It should be yellow, or green.