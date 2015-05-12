.. _policy:

Development and Core Tool Policy
================================

Development and Core Tool Policy
--------------------------------

In order to prevent core tools being used by developers being incompatible,
rendering installation instructions buggy and/or causing random failures in a
complicated build environment, everyone on the DIMS project **must** use the
same core tools by policy.  This will allow controlled updates and provide
stability in the tools we are using within the project.  Far too much time is
wasted when one person can do something, but another can't and gets blocked, or
something runs fine on one system, but fails on another system.

.. _developingongithub:

Developing on a fork from GitHub
--------------------------------

In this section, we will go through the steps for using Hub Flow for
developing on a branch forked from GitHub, publishing the results back
to GitHub for others to share.

For this example, there has already been a fork made on GitHub.  Start by
cloning it to your local workstation: ::

    [dittrich@localhost git (master)]$ git clone https://github.com/uw-dims/sphinx-autobuild.git
    Cloning into 'sphinx-autobuild'...
    remote: Counting objects: 366, done.
    remote: Total 366 (delta 0), reused 0 (delta 0)
    Receiving objects: 100% (366/366), 62.23 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (180/180), done.
    Checking connectivity... done.
    [dittrich@localhost git (master)]$ cd sphinx-autobuild/
    [dittrich@localhost sphinx-autobuild (develop)]$ git branch -a
    * develop
      remotes/origin/HEAD -> origin/develop
      remotes/origin/develop
      remotes/origin/feature/1-arbitrary-watch
      remotes/origin/feature/tests
      remotes/origin/master
    [dittrich@localhost sphinx-autobuild (develop)]$ git checkout master
    Branch master set up to track remote branch master from origin by rebasing.
    Switched to a new branch 'master'
    [dittrich@localhost sphinx-autobuild (master)]$ git branch -a
      develop
    * master
      remotes/origin/HEAD -> origin/develop
      remotes/origin/develop
      remotes/origin/feature/1-arbitrary-watch
      remotes/origin/feature/tests
      remotes/origin/master
    [dittrich@localhost sphinx-autobuild (develop)]$ ls
    AUTHORS				NEWS.rst			fabfile.py			requirements-testing.txt
    CONTRIBUTING.rst		README.rst			fabtasks			requirements.txt
    LICENSE				docs				pytest.ini			setup.py
    MANIFEST.in			entry-points.ini		requirements-dev.txt		sphinx_autobuild

..

.. warning:: NOT FINISHED YET...

Developing
----------

Developing new features for the DIMS CI Utilities...

.. warning:: NOT FINISHED YET...

