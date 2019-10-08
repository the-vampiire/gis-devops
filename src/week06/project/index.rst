.. _week4_project:

==================================================
Week 6 - Project Week: Zika Mission Control Part 3
==================================================

Project Requirements
====================

Following are the requirements from our stakeholders, and our tech team.

Stakeholder Requirements
------------------------

- Application is live via a web browser
- Application includes a fuzzy search

Technical Requirements
----------------------

- All AWS resources are to be secured within a VPC
- Access outside of the VPC should be restricted to one web server on port 80
- Separate EC2 for web app and Elasticsearch
- Database should use AWS RDS

Primary Objectives
==================

You should **complete all primary objectives** before working on any secondary objectives.

For your primary objectives articles have been provided to help you think about what needs to be completed to pass the objective.

0. :ref:`week6-aws-infrastructure`
1. :ref:`week6-rds-setup`
2. :ref:`week6-web-app-setup`
3. :ref:`week6-elasticsearch-setup`

Secondary Objectives
====================

It takes a lot of work to manually configure AWS and to build, and deliver artifacts to our web server. Our bonus objectives revolve around automating the CI/CD pipeline.

- Use S3 to deliver your jar file
- Use Jenkins to test, build, and deliver your web application
- Setup an auto-scaling group for your web app so that it scales under load
- Configure Sonarqube to improve the quality of your code
- Create and use a CloudFormation script to automate the infrastructure creation of your project

Turning in Your Work
====================

Code Review
-----------

Let your instructor know When you complete the primary objectives. The instructor will need a link to your GitLab repo, and they will peform a code review, and leave you feedback.

Objective Checklist
-------------------

As you work through the objectives for this week, keep track of them on your Checklist, your instructor will also confirm which objectives you completed in their code review. If you don't pass an objective the instructor will give you feedback on what you need to do to complete that objective.

Presentation
------------

Friday afternoon, everyone will present their project to the class. This presentation is meant to be a celebration of your hard work throughout the week, and as a chance for you to share, and learn from the other students in the class.

At the end of this course, during your graduation ceremony you will be expected to present your final project to the attendees. Every project week we will have a presentation as a way for you to practice for this final presentation.

Check Your Knowledge
====================

To reinforce your understanding of the concepts answer these questions to yourself:
    - What is a VPC? Why is it beneficial to use one?
    - How do you control access to your various AWS resources?
    - What resources are publicly available?
    - Which resources are not publicly available?
    - Should you restrict access to any of your currently public resources?
    - How does a user interface with your database? elasticsearch?
    - What are the various means of project delivery we used this week?
    