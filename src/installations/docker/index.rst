:orphan:

.. _docker:

====================
Installation: Docker
====================

What is Docker
--------------

Docker is a containerization tool. A container is a standardized unit of software, such as Postgres, ElasticSearch, or GeoServer, etc. We will be installing some things inside of a container so that they are separate from the rest of our computer, and so that we have a greater level of control over the software in our containers.

We are using containers for a few different reasons

* Control - we can leverage docker's built in tools to stop, start, and download software
* Customization - we can have multiple versions of tools installed, and they can each be configured differently
* Isolation - each container is stored in a unique location on your computer
* Mimics cloud computing - Communication between containers, and applications is similar to how they operate in the cloud

In this class we will have a lesson on Docker where we will go into greater detail on Docker as a subject. However that is in the later part of this course. You will use Docker before that lesson, and this guide will help you install it.

Installation Steps: Mac
-----------------------

Docker mac installation steps

1. Sign up for a free `Docker Hub account <https://hub.docker.com/signup>`_
2. Navigate to `Docker Hub <https://hub.docker.com/editions/community/docker-ce-desktop-mac>`_
3. Download the Docker Desktop for Mac .dmg file
4. Open the .dmg file to install Docker Desktop for Mac
5. After installation run ``$ docker --version`` to verify docker installed correctly