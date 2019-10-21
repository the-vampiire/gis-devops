:orphan:

.. _walkthrough-docker:

=========================
An Introduction to Docker
=========================

Follow along with the instructor as you explore Docker and configure a basic Flask application.

Docker Commands
===============

* ``docker ps`` see list of **running** containers
* ``docker ps -a`` see lisf of all containers, including ones that failed or were stopped
* ``docker start <container-name or id>`` starts the container
* ``docker stop <container-name or id>`` stops the container
* ``docker restart <container-name or id>`` restarts the container
* ``docker rm <container-name or id>`` removes the container
* ``docker images`` shows list of images that you have downloaded. containers are created from images
* ``docker image rm <image-name or id>`` removes an image
* ``docker build --tag super-happy-fun-os .`` builds a new image that containers can be created from
* ``docker-compose up -d`` uses a docker-compose.yml file to configure and start containers
* For more info and more commands please see `the Docker CLI docs <https://docs.docker.com/engine/reference/commandline/docker/>`_

Setup
=====

1. Make sure that you have Docker installed. You can check the Docker installation by running

::

   $ docker --version

2. Check if ``pip`` is installed (package manager for python)

::

   $ pip --version

If ``pip`` is not found, install it. 

::
   
   $ sudo easy_install pip

3. Clone this repo `docker-flask-walkthrough <https://gitlab.com/LaunchCodeTraining/docker-flask-walkthrough>`_

4. ``cd`` into the folder of the repo you just clonded

5. Install dependencies using pip by running:

::

   $ pip install --user -r requirements.txt

A simple Python web app
=======================

In this walkthrough we are going to run a simple Python web app. The app uses Python Flask as a web server. Flask may be new to you, but see if you see any similarities to Spring Boot.

In Visual Studio Code, or another general purpose editor, open the file ``simple_app.py``

Review file ``simple_app.py``

::

  import time

  from flask import Flask


  app = Flask(__name__)


  @app.route('/')
  def hello():
      return 'Hello World!'

  if __name__ == "__main__":
      app.run(host="0.0.0.0", debug=True)

Run the simple web app by running the below command

::

  $ python simple_app.py

Navigate to ``http://localhost:5000``, it should show our simple message.

Now **stop** this process in your terminal, using ``ctrl + c`` as we will next get this running via a Docker container.

Create Dockerfile
-----------------

We need to setup a ``Dockerfile`` that uses CentOS to run our webapp.


Paste in the below text into file ``/docker-flask-walkthrough/Dockerfile`` (please review the content instead of only pasting it)

::

  # start with the centos 7 base image
  FROM centos:7

  # ADD <source> <destination>, Adds the current directory to /code in the container
  ADD . /code

  # install and upgrade software we need on the container
  RUN yum -y install epel-release
  RUN yum -y update
  RUN yum -y install python-pip
  RUN pip install --upgrade pip
  RUN pip install -r /code/requirements.txt

  # Run the web app as the main process (there can only be one CMD per Dockerfile)
  CMD ["python", "/code/simple_app.py"]

We need to build a Docker image that will run our simple web app. 

* In terminal go to ``/docker-flask-walkthrough``
* Run this command

::

  (this may take a few minutes)
  $ docker build --tag my-centos-simple .

You Created a Docker Image
++++++++++++++++++++++++++

The ``Dockerfile`` in the previous section along with the command ``docker build`` created a new docker image that can be used to create containers with.

View the list of docker images

::

  (your new image my-centos-simple should appear in the list)
  $ docker images

Create a Container
++++++++++++++++++

Run these commands to create a container that using the new image. Remember an images don't run, the are the OS and foundation used by running conatiners.

::

  $ docker create -i -t -p 5000:5000 my-centos-simple
  $ docker start <container_name/id>
  (to see list of conatiners)
  $ docker ps -a

Check the browser to see if the "Hello World" message shows up. ``http://localhost:5000``

Now stop that docker container by running::

  (the last number is the id for the docker container)
  $ docker stop 8b54229210c9

A more complex Python app
-------------------------

In the next section of the walkthrough, we are going to stand up a more complex Flask app. In this app, we are going to integrate the key-value database Redis. In order to integrate Redis into the Flask web app, we will need to leverage Docker's network capabilities.

Review ``counter_app.py``:::

  import time

  import redis
  from flask import Flask


  app = Flask(__name__)
  cache = redis.Redis(host='redis', port=6379)


  def get_hit_count():
      retries = 5
      while True:
          try:
              return cache.incr('hits')
          except redis.exceptions.ConnectionError as exc:
              if retries == 0:
                  raise exc
              retries -= 1
              time.sleep(0.5)


  @app.route('/counter')
  def hello():
      count = get_hit_count()
      return 'Hello World! I have been seen {} times.\n'.format(count)

  if __name__ == "__main__":
      app.run(host="0.0.0.0", debug=True)


Create Redis Container
----------------------

We don't want our users to have to install redis on their own. We need to create a container that runs redis. Then we can link the ``redis`` and ``counter-app`` containers using ``docker-compose``. Sounds fun right?

Find and Download the Redis Image

* Go to `Docker Hub <https://hub.docker.com/>`_ and search for ``redis``. 
* Click on the official ``redis`` result. 
* Click the **tags** tab.
* We are going to use the ``redis:alpine`` tag. 
  
  * Tags refer to a specfic version of redis, details are available on the docker site.

* Pull in a copy of the ``redis:alpine`` image to your computer by running

::

  $ docker pull redis:alpine

Create counter-app Image
------------------------

1. Change the last line in the ``Dockerfile`` to be::

    CMD ["python", "/code/counter_app.py"]

2. Build the ``centos-counter-app`` image with this command::

   $ docker build --tag centos-counter-app .

.. note::

  The above command takes a while to run. After it completes you will see the below message:

::

  Successfully built 8447bcee9c62
  Successfully tagged centos-counter-app:latest

3. Verify it was built by viewing docker images ``$ docker images``

Docker Compose File
-------------------

We are going to bring this all together by creating  a ``docker-compose.yml`` file, that will allow the Flask app to reference the Redis container.

Paste this text into ``docker-compose.yaml``
::

  version: '3'
  services:
    web:
      image: "centos-counter-app"
      ports:
      - "5000:5000"
    redis:
      image: "redis:alpine"

Use the following command2 to stand up and verify the two containers

1. Run ``$ docker-compose up -d``

::

  Creating docker-flask-walkthrough_redis_1 ... done
  Creating docker-flask-walkthrough_web_1   ... done

2. Verify that the containers are running ``$ docker ps``
3. Navigate to ``http://localhost:5000/counter``

Docker Logs
-----------

Let's look at these containers a bit more indepth. ``docker logs {container name}`` will show all of the logs that have been written to STDOUT. (replace {container name} with the actual container name).::

  $ docker logs {container name/id}

Let's also take the container details. ``docker inspect {container name/id}`` will show all of the details about the container including network information.::

  $ docker inspect {container name/id}
