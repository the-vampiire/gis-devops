:orphan:

.. _docker-psql:

===================================
Installation: Docker PSQL Container
===================================

Installation Steps: Mac
-----------------------

0. Disable psql through brew (if necessary)
1. Create an env.list file
2. Create container
3. Connect to psql

Disable PSQL through brew
+++++++++++++++++++++++++

In the prep work for this class, or on your own, you may have installed PSQL through the MacOS package manager Homebrew.

Before you can install PSQL in a new docker container you will need to disable any versions of PSQL that are currently running as a brew service.

Check your current brew services by running ``$ brew services list`` from the terminal.

If you see any services that are running with the name PSQL, or Postgres stop them with the brew services stop command ``$ brew services stop PSQL``.

After stopping all PSQL services in brew rerun ``$ brew services list`` to ensure they stopped correctly.

After you have verified all versions of PSQL have stopped through brew, you may continue with the rest of the installation instructions.

Create env.list
+++++++++++++++

Before we create our container we first need to create a temporary file that will create our containers first user, and configure the container to listen for requests.

* Create a new file in your terminal ``$ nano env.list``
* Add ``POSTGRES_USER=psql_user`` to env.list
* Add ``ALLOW_IP_RANGE=<0.0.0.0/0>`` to env.list
* Save env.list

This small file will have postgres create a new user named psql_user, and allow all IP addresses to make requests to the container.

Create Container
++++++++++++++++

We can now create, and run our container with ``$ docker run --name "postgres" -p 5432:5432 -d -t --env-file ./env.list postgres:9.4``

* ``docker run`` runs a command in a container (in this case it creates the container since it doesn't already exist)
* ``--name`` allows us to name our container (in this case we are naming our container "postgres")
* ``-p`` allows us to *publish* a port (host_port:container_port in this case both are the psql default 5432)
* ``-d`` starts our container in *detach* mode which means it will run in the background
* ``-t`` allows us to run a terminal command (in this case adding the contents of our env.list file)
* ``--env-file ./env.list`` the type of file, and the file from our computer
* ``postgres:9.4`` the image of the container (in this case postgres version 9.4)

This command creates a container named "postgres" with the postgres:9.4 image that listens on port 5432, is detached from this command, and we are running a command to add the contents of env.list to our new "postgres" container.

If you run ``$ docker ps -a`` now you should see a new container named "postgres".

Connect to psql
+++++++++++++++

Now we will want to connect to postgres to make sure it installed correctly. To do this you will need the psql interactive terminal installed on your computer. This comes with every installation of Postgres. You can check that you have this by typing ``$ postgres --version`` to verify postgres is installed on your computer.

If it isn't installed you will want to check out `Postgres Installation <../../installations/postgres/>`_ to install Postgres.

Once you have Postgres installed on your local computer, and you have created the new "postgres" container we can connect to our "postgres" container by typing ``$ psql -h 127.0.0.1 -p 5432 -U psql_user -d postgres`` into our terminal. This command will drop you into psql.

* ``psql`` The command to access the PSQL Interactive Terminal
* ``-h 127.0.0.1`` The ip address of the host we are connecting to (in this case it's localhost)
* ``-p 5432`` The port of the machine we are accessing (in this case the psql default)
* ``-U psql_user`` The user that is requesting access to the database (in this case the user we added with the env.list file)
* ``-d postgres`` The name of the database we want to access (in this case the default database created by postgres)