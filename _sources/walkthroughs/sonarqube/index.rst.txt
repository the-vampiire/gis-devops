:orphan:

.. _walkthrough-sonarqube:

======================
Walkthrough: Sonarqube
======================

Follow along with the instructor as we configure Sonarqube.

Setup
=====

We will be setting up Sonarqube in our airwaze project. You can try it out on any of your current branches.

Continuous Inspection
=====================

Sonarqube is an open source tool for continuous inspection of code quality including: bug detection, code smells, and security vulnerabilities.

We will use Sonarqube to analyze our Java code. It will search for duplicate code, unused variables/parameters, code smells, and much more. Fixing the issues found by sonarqube helps to
keep our code consistent between developers and hopefully more secure and stable. Consider sonarqube to be Eslint for Java, with more features. Note that sonarqube will analyze multiple language, however for this class we are only using it for Java.

Sonarqube runs as a service. Project configuration and reports are viewable in your browser via web interface. Defaults to ``http:localhost:9000`` if installed locally.

Sonarqube can integrate with other tools such as gradle and jenkins.

Install and Configure Sonarqube
===============================

* Install sonarqube as a Docker container with: ``docker run --name sonarqube -p 9000:9000 sonarqube``
* Now go to this address in your browser: ``http://localhost:9000``
* Login with default user. username: admin password: admin (<- example of not changing defaults. very bad!)
* Click ``Create new project`` button
* Enter ``airwaze`` as the project key & click ``Set up``.
* Enter ``airwaze`` as the name for your token & click ``Generate``.
* Click ``Continue``
* Select ``Java``
* Select ``Gradle``
* Add the provided snippet to your ``build.gradle`` file
* Run the provided gradlew command in your terminal from the root of your airwaze project
* Navigate to ``http://localhost:9000/projects`` to see your project analysis.

This gives you an overview of your code analysis. It will provide you with information on how much code coverage you have, how clean your code is, known vulnerabilities, and more.

This is another tool we can use to improve the quality of our code.