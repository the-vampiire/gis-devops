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
2. Instructor will add a file to the repo using a branch and a Merge Request
3. In your terminal, clone that repo
4. Create a new branch. Example: blakes-branch
5. Checkout the new branch
6. Creat a new file that includes your name in the filename. Example: blake.txt
7. Stage and commit your change
8. Push your branch to origin
9. Go to gitlab.com and create a Merge Request for your branch
10. Have another student merge your Merge Request
11. Checkout master locally
12. Do a get pull to see your changes now in master

Part 2
------
1. The instructor will create a new branch that incluedes a roster.txt file
2. That branch will be merge into master
3. Everyone in the class should pull to get the commit containing that file

  .. note::
    Instructor should do these next steps first

4. Create and switch to a new branch
5. Add your name to the roster.txt file
6. Stage, commit, and then push their changes
7. You should get a merge conflict
8. Resolve the conflict by editing the file that has the conflict
9. Run git status in terminal
10. Run git add roster.txt to mark the conflict as resolved
11. After resolving your conflicts you can push your changes to origin

.. _git-reference:

Git Reference
-------------

Switch to an Existing Branch
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. ``git fetch`` to make sure you have an updated list of branches
#. ``git branch -a`` show a list of all existing branches
#. ``git checkout BRANCH-NAME`` will checkout the branch BRANCH-NAME

Create a New Branch
^^^^^^^^^^^^^^^^^^^

#. ``git status`` to make sure you are on the branch you want to make a branch from
#. ``git checkout -b NEW-BRANCH-NAME`` creates branch NEW-BRANCH-NAME and switches to it

Commit and Push
^^^^^^^^^^^^^^^

How to commit and push your changes to a remote repo.

#. ``git status`` to review branch and files that have changed
#. ``git add .`` stage your changes
#. ``git commit -m "a short message about what you changed"``
#. ``git push`` push commit to remote repo

Other Helpful Commands
^^^^^^^^^^^^^^^^^^^^^^

* ``git log`` see list of commits on your current branch
* ``git pull`` pull in the latest changes for your current branch from origin
* ``git fetch`` make your local repo aware of any new changes on origin
* ``git reset --hard`` WARNING!! Removes all local changes that have NOT been committed 
