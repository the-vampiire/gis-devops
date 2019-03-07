:orphan:

.. _environment-variables-intellij:

===============================================
Configuration: Environment Variables - IntelliJ
===============================================

Environment Variables
---------------------

An environment variable is a variable that is declared *outside* of the code of our project. We then load this outside variable into our project using a *token*.

Environment variables provide two major benefits:
    * Protect sensitive data
    * Code doesn't need to be altered for different environments

Many projects need to include sensitive data: usernames, passwords, IP addresses, etc, instead of including this data in our project we use environment variables.

In this configuration we will learn how to create new environment variables in IntelliJ, and we will learn the syntax for calling those variables in our code.

Create Environment Variable
---------------------------

We add environment variables to our run time configurations.

Click on the dropdown box for your runtime configurations.

Select the runtime you want to add environment variables to.

Click edit.

Click environment variables folder icon to pull up the environment variables screen.

From here you can add as many environment variables as you want. You do this by hitting the little plus sign in the top right corner of this box.

In this case I'm going to store the information about my database. This includes the IP address, the port, the username, and the password for my database. This is information I don't want to push to github, and that I don't want in my project.

Now that I have create my environment variables I need to use them.


Syntax
------

In IntelliJ we can use our environment variables by using Tokens. IntelliJ recognizes this token as an environment variable and replaces the content of your environment variable in place of the token.

In this case I will be using the DataBase environment variables to configure this application with my database.

${APP_DB_HOST}
${APP_DB_PORT}
${APP_DB_USER}
${APP_DB_PASS}
${APP_DB_NAME}

spring.datasource.url=jdbc:postgresql://${APP_DB_HOST}:${APP_DB_PORT}/${APP_DB_NAME}
spring.datasource.username=${APP_DB_USER}
spring.datasource.password=${APP_DB_PASS}
spring.jpa.hibernate.ddl-auto=update



