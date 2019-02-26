:orphan:

.. _walkthrough-gitLab:

====================================
Walkthrough: Git and Gitlab
====================================



Follow along with the instructor as we review Git and Gitlab.

We will be using Git, and GitLab almost everyday throughout this class. This walkthrough will show you some of the basics. Primarily creating new projects, creating new branches, creating merge requests for branches, and resolving merge conflicts.

Setup
-----

* You will need a Gitlab account (don't worry it's free)
* Go to `gitlab.com <http://gitlab.com and login>`_

Follow Along as we...
---------------------

Part 1 - Instructor Steps
-------------------------
1. The instructor will create a new project on Gitlab for this walkthrough

.. image:: /_static/images/gitlab-create-new-project.png

.. image:: /_static/images/gitlab-create-new-project2.png

2. Instructor will add a file to the repo using a branch and a Maerge Request
  
  * First the instructor will need to create new branch
  
  .. image:: /_static/images/gitlab-create-new-branch.png
  
  * Then the instructor will have to add a new file

  .. image:: /_static/images/gitlab-create-new-file.png

  * Then the instructor will need to commit and push this new file

  .. image:: /_static/images/gitlab-add-commit-push.png

  * Then the instructor will create a new merge request

  .. image:: /_static/images/gitlab-merge-request-button.png

  .. image:: /_static/images/gitlab-merge-request-form1.png

  The first part of this form tells us we are merging the new_file branch into the master branch. With every merge a commit is made, so you have the option to change the commit title, or message. There are other options in the Merge Request form such as assigning this merge request to a coworker, or fellow student. Nothing needs to be changed for now, so the instructor can scroll down to the submit merge request button.

  .. image:: /_static/images/gitlab-merge-request-form2.png

3. Instructor will merge the new request into the master branch

  * Since there aren't any merge conflicts this can be done automatically by clicking the merge button

  .. image:: /_static/images/gitlab-merge.png

  * After our merge has completed we can see our master branch now contains my_new_file.txt the file added in the branch of the merge request

  .. image:: /_static/images/gitlab-merged-to-master.png

Part 1 - Student Steps
----------------------

1. In your terminal, clone the repo your instructor created
2. Create a new branch. Example: blakes-branch
3. Checkout the new branch
4. Create a new file that includes your name in the filename. Example: blake.txt
5. Stage and commit your change
6. Push your branch to origin
7. Go to gitlab.com and create a Merge Request for your branch
8. Have another student merge your Merge Request
9. Checkout master locally
10. Do a git pull to see your changes now in master

Part 2 - Instructor Steps
-------------------------

1. The instructor will create a new branch that includes a roster.txt file
  
  .. image:: /_static/images/gitlab-roster.png

2. That branch will be merged into master

  .. image:: /_static/images/gitlab-roster-to-master.png

  .. image:: /_static/images/gitlab-roster-merged.png


Part 2 - Student Steps
----------------------

1. Get the updated code by pulling in master `git pull origin master`
2. Create and switch to a new branch `git checkout -b blakes-roster-branch`
3. Add your name to the roster.txt file
4. Stage, commit, and then push your changes
5. You should get a merge conflict
6. Resolve the conflict by editing the file that has the conflict
7. Run `git status` in your terminal
8. Run `git add roster.txt` to mark the conflict as resolved
9. After resolving your conflicts you can push your changes to origin

