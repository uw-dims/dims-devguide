.. _sourcemanagement:

Source Code Management with Git
===============================

Daily development work on DIMS source code is done using a local server
accessed via SSH to ``git.devops.develop``.  The public release of DIMS
software will be from `github.com/uw-dims`_ with public
documentation  delivered on `ReadTheDocs`_. (DIMS documentation is covered in
Section :ref:`documentation`.)

.. note::

    At this point `github.com/uw-dims`_ primarily contains forked
    repositories of the software described in Section :ref:`installingtools`.

..

Team members need to have familiarity with a few general task sets,
which are covered in the sections below. These tasks include things like:

+ Cloning repositories and initializing them for use of the
  ``hub-flow`` Git addon scripts.

+ On a daily basis, updating repositories, creating feature
  or hotfix branches to work on projects, and finishing those branches after
  testing is complete to merge them back into the ``develop`` branch.

+ Creating new repositories, setting triggers for post-commit actions,
  and monitoring continuous integration results.

+ Keeping up to date with new repositories (or starting fresh with a new
  development system by cloning all DIMS repositories a new.)

.. attention::

    Every now and then, you may do something with Git and immediately
    think, "Oh, no! I did *not* want to do *that*..." :(

    There are resources on Dave Dittrich's home page in the
    :ref:`dittrich:usinggit` section.  Two good resources for learning how things
    work with Git (and how to undo them) are:

    + `How to undo (almost) anything with Git`_, GitHub blog post by jaw6, June 8, 2015
    + `Undo Almost Anything with Git webinar`_, YouTube video by Peter Bell and Michael Smith, February 11, 2014

..

.. _How to undo (almost) anything with Git: https://github.com/blog/2019-how-to-undo-almost-anything-with-git
.. _Undo Almost Anything with Git webinar: https://youtu.be/oUzbaCRoeFA

.. _gitfoundation:

Foundational Git Resources
--------------------------

+ `Yan Pritzker's Git Workflows book`_
+ `The Thing About Git`_
+ `Commit Often, Perfect Later, Publish Once: Git Best Practices`_
+ `Git Tips`_
+ `git-flow`_ utilities to follow Vincent Dreisen branching workflow
+ `HubFlow`_ (GitFlow for GitHub)

.. _Yan Pritzker's Git Workflows book: http://documentup.com/skwp/git-workflows-book
.. _The Thing About Git: http://tomayko.com/writings/the-thing-about-git
.. _Commit Often, Perfect Later, Publish Once\: Git Best Practices: http://sethrobertson.github.io/GitBestPractices/
.. _Git Tips: http://mislav.uniqpath.com/2010/07/git-tips/
.. _git-flow: http://danielkummer.github.io/git-flow-cheatsheet/
.. _HubFlow: http://datasift.github.io/gitflow/

The need for policy and discipline
----------------------------------

Git is a great tool for source management, but can be a little tricky to use
when there is a team of programmers all using Git in slightly different ways.
Bad habits are easy to form, like the short-cut of working on the ``develop``
branch in a multi-branch workflow.

Figure :ref:`vincentdriessenbranching` comes from Vincent Driessen's "`A
successful Git branching model`_".  The DIMS project is following this model as
best we can to maintain consistency in how we create and use branches. The
general policy is to derive branch names from Jira tickets, in order to keep
information about why the branch exists, who is responsible for working on it,
and what is supposed to be done on the branch, in a system that can track
progress and prioritization of activities within sprints.

.. _A successful Git branching model: http://nvie.com/posts/a-successful-git-branching-model/

.. _vincentdriessenbranching:

.. figure:: images/git-model@2x.png
   :alt: Vincent Driessen Git branching model
   :width: 80%
   :align: center

   Vincent Driessen Git branching model

..

Because public release of source code will be through GitHub,
the ``hubflow`` tool was chosen for use within the project.
Take a moment to read through the following Gist (original source:
`bevanhunt/hubflow_workflow`_), just to get an overview of ``hubflow``
concepts. This Gist provides an overview of ``hubflow`` branch concepts and
some other things about Git that are good to keep in mind, but this is *not*
the totality of information in this guide about using ``hubflow`` (keep reading
further down for more DIMS-specific examples of using ``hubflow`` commands).

.. code-block:: none

    Git Hubflow Workflow:

    Sync Branch:
    git hf update - this will update master and develop and sync remote branches withlocal ones (be sure not to put commits into develop or master as it will push these up)
    git hf push - this will push your commits in your local branch to the matching remote branch
    git hf pull - this will pull the remote commits into your local branch (don't use if the remote branch has been rebased - use git pull origin "your-branch" instead)

    Feature Branch:
    gif hf feature start "my-feature" - this will create a feature branch on origin and local will be based off the latest develop branch (make sure to git hf update before or you will get an error if local develop and remote develop have divereged)
    git hf feature finish "my-feature" - this will delete the local and remote branches (only do this after a PR has been merged)
    git hf feature cancel -f "my-feature" - this will delete the local and remote branches (only do this if the feature branch was created in error)
    git hf feature checkout "my-feature" - this will checkout the feature branch

    Hotfix Branch:
    git hf hotfix start "release-version" - this will create a hotfix branch on origin and local will be based off the latest develop branch (make sure to git hf update before or you get an error if local develop and remote devleop have divereged)
    git hf hotfix finish "release-version" - this will delete the local and remote branches and merge the commits of the hotfix branch into master and develop branches - it will also create a release tag that matches the release version on master
    git hf hotfix cancel -f "release-version" - this will delete the remote and local branch (only do this if the hotfix was created in error)
    git checkout hotfix/"release-version" - this will checkout the hotfix branch (make sure to git hf update first)

    Release Branch:
    git hf release start "release-version" - this will create a release branch on origin and local will be based off the latest develop branch (make sure to git hf update before or you get an error if local develop and remote devleop have divereged)
    git hf release finish "release-version" - this will delete the local and remote branches and merge the commits of the release branch both into develop and master - it will also create a release tag that matches the release version on master
    git hf release cancel -f "release-version" - this will delete the local and remote branch (only do this if the release was created in error)
    git checkout release/"release-version" - this will checkout the release branch (make sure to git hf update first)

    Preparing a PR:
    - put the Aha! Ticket # in PR title with a description
    - assign to the proper reviewer
    - don't squash the commits until after reviewed
    - after review - squash the commits

    Squashing Commits:
    - checkout the branch you want to squash
    - git merge-base "my-branch" develop (returns merge-base-hash)
    - git rebase -i "merge-base-hash"
    - change all commit types to "squash" from "pick" in the text file (except first) & save file
    - if you get a no-op message in the text file and still have multiple commits then use the command git rebase -i (without the hash)
    - fix any merge conflicts
    - you should have one commit
    - force update your remote branch: git push origin "my-branch" -f

    Resolving merge conflicts with the develop branch that are not squashing related (generally on PRs - auto-merge will show as disabled):
    - git hf update
    - git rebase develop (while in your branch)
    - resolve any merge conflicts

    Rules to remember:
    - don't ever git merge branches together manually (should never run command - git merge)
    - squash only after review and before merging PR into develop

..

..

.. _bevanhunt/hubflow_workflow: https://gist.github.com/bevanhunt/903740bf7306d806f943


.. note::

    There is a large body of references on Git that are constantly being
    updated in the `Software Development>Git`_ section of Dave Dittrich's web
    page.

..

.. _Software Development>Git: https://staff.washington.edu/dittrich/home/swdev.html#git


.. caution::

    Mac OS X (by default) uses a *case insensitive* HFS file system.
    Unlike Ubuntu and other Linux/Unix distributions using case-sensitive
    file systems like ``ext2``, ``reiserfs``, etc., the default OS X file
    system does not care if you name a file ``THISFILE`` or ``ThisFile``
    or ``thisfile``. All of those refer to the same file on a Mac. This can cause
    problems when you use Git to share a source repository between computers
    running OS X, Windows, and/or Linux, because what Linux thinks are two
    files, the Mac only thinks is one (and that really causes problems for Git).

    See `Git on Mac OS X: Don't ignore case!`_
    and `How do I commit case-sensitive only filename changes in Git?`_. A solution
    for Mac OS X, posted in `Case sensitivity in Git`_, is documented in
    Section :ref:`macosxcasesensitive`.

..

.. _Git on Mac OS X\: Don't ignore case!: http://tapestryjava.blogspot.com/2010/07/git-on-mac-os-x-dont-ignore-case.html
.. _How do I commit case-sensitive only filename changes in Git?: http://stackoverflow.com/questions/17683458/how-do-i-commit-case-sensitive-only-filename-changes-in-git
.. _Case sensitivity in Git: http://stackoverflow.com/questions/8904327/case-sensitivity-in-git


.. _gitconfiguration:

Global Git Configuration
------------------------

As we learn about best practices, the following set of global configuration
settings will be updated. Refer back to this page, or look in the ``dims-git``
repo, for the latest configuration examples.

The following are user-specific settings that you should alter for your own account and preferences of editor/merge method:

.. code-block:: none

    $ git config --global user.name "Dave Dittrich"
    $ git config --global user.email "dittrich@u.washington.edu"
    $ git config --global merge.tool vimdiff
    $ git config --global core.editor vim

..

.. TODO(dittrich): This needs to be fixed.

.. caution::


    There is a bad side-effect of the way the initial common Git configuration
    were managed using Ansible.  Whenever the ``dims-users-create`` role was
    played, a fresh copy of the user's global Git configuration file
    (``~/.gitconfig``) is created, over-writing whatever the user had created
    by issuing the commands above and forcing the user to have to re-issue
    those commands every time the play was run.  (See the file
    ``$GIT/ansible-playbooks/dims-users-create/templates/gitconfig.j2``).
    That is a bug in that it is not `idempotent`_.

    One quick hack that restores these values is to add those commands to
    your ``$HOME/.bash_aliases`` file, which is run every time a new
    interactive Bash shell is started.

    A better long-term solution, which we are working towards, is to
    have the ``user.name`` and ``user.email`` configuration settings come
    from the Trident portal user attributes table, so they can be
    set by the user and stored in one central location, which can then be
    retreived from the Trident user database and applied consistently
    by Ansible when it sets up user accounts.

..

.. _idempotent: http://docs.ansible.com/ansible/glossary.html#idempotency


The following are general and can be applied to anyone's configuration
(included here without a prompt so you can cut/paste to a command
line):

.. code-block:: bash

    git config --global push.default tracking
    git config --global core.excludesfile ~/.gitignore_global
    git config --global core.autocrlf false
    git config --global color.diff auto
    git config --global color.status auto
    git config --global color.branch auto
    git config --global color.interactive auto
    git config --global color.ui auto
    git config --global branch.autosetuprebase always

..

The following are convenience aliases that help with certain tasks:

.. code-block:: bash

    git config --global alias.find 'log --color -p -S'
    git config --global alias.stat 'status -s'
    git config --global alias.unstage "reset HEAD --"
    git config --global alias.uncommit "reset --soft HEAD^"
    git config --global alias.gr 'log --full-history --decorate=short --all --color --graph'
    git config --global alias.lg 'log --oneline --decorate=short --abbrev-commit --all --color --graph'
    git config --global alias.log1 'log --oneline --decorate=short'

..

.. todo::

    Work out which other convenience alises would be useful for
    DIMS development from these links:

    + `One weird trick for powerful Git aliases`_, by Nicola Paolucci, October 3, 2014
    + GitHubGist `mwhite/git-aliases.md`_ (The Ultimate Git Alias Setup)
    + `Must Have Git Aliases: Advanced Examples`_, by @durdn

    See Jira ticket `DIMS-470`_. (Remove this when completed.)

..

.. _DIMS-470: http://jira.devops.develop/browse/DIMS-470

.. _One weird trick for powerful Git aliases: http://blogs.atlassian.com/2014/10/advanced-git-aliases/
.. _mwhite/git-aliases.md: https://gist.github.com/mwhite/6887990
.. _Must Have Git Aliases\: Advanced Examples: http://durdn.com/blog/2012/11/22/must-have-git-aliases-advanced-examples/


.. _dailygittasks:

Daily tasks with Git
--------------------

This section covers regular tasks that are performed to
work with source code using Git. This section assumes you are
using the ``hub flow`` tool described in Section :ref:`installingtools`.

.. warning::

   These tools are being installed in the ``dimsenv`` Python virtual
   environment to make it easier for everyone on the team to access them and to
   stay up to date with instructions in this document. If you have `any`
   problems, file a `Jira
   <http://jira.devops.develop/secure/Dashboard.jspa>`_ ticket or talk
   to Dave immediately upon encountering a problem. Do not let yourself get
   blocked on something and block everyone else as a result!

..

.. _updatinglocalrepos:

Updating local repos
~~~~~~~~~~~~~~~~~~~~

The most common task you need to do is keep your local Git repos up to date
with the code that others have pushed to remote repositories for sharing.
With several dozen individual Git repos, keeping your system up to date
with all of these frequently changing repos using ``git`` commands alone
is difficult.

To make things easier, helper programs like the ``hubflow`` scripts
and ``mr`` can be used, but even those programs have their limits.

The preferred method of updating the larger set of DIMS Git repos
is to use ``dims.git.syncrepos``, which in turn calls ``hubflow`` via
``mr`` as part of its processing. This convenience script (described in
Section :ref:`dimsgitsyncrepos`) works on many repos at once, saving time and
effort.

You should still learn how ``hubflow`` and ``mr`` work, since you will
need to use them to update individual Git repos when you are working within
those repos, so we will start with those tools.

.. _updatingwithhubflow:

Updating using ``hubflow``
""""""""""""""""""""""""""

The following command ensures that a local repo you
are working on is up to date:

.. note::

   The list of actions that are performed is provided at the end of the command
   output. This will remind you of what all is happening under the hood of Hub
   Flow and is well worth taking a few seconds of your attention.

..

.. code-block:: none

    $ git hf update
    Fetching origin
    remote: Counting objects: 187, done.
    remote: Compressing objects: 100% (143/143), done.
    remote: Total 165 (delta 56), reused 1 (delta 0)
    Receiving objects: 100% (165/165), 31.78 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (56/56), completed with 13 local objects.
    From git.devops.develop:/opt/git/ansible-playbooks
       001ba47..0e12ec3  develop    -> origin/develop
     * [new branch]      feature/dims-334 -> origin/feature/dims-334
    Updating 001ba47..0e12ec3
    Fast-forward
     docs/source/conf.py                       | 2 +-
     roles/dims-ci-utils-deploy/tasks/main.yml | 5 +++++
     2 files changed, 6 insertions(+), 1 deletion(-)

    Summary of actions:
    - Any changes to branches at origin have been downloaded to your local repository
    - Any branches that have been deleted at origin have also been deleted from your local repository
    - Any changes from origin/master have been merged into branch 'master'
    - Any changes from origin/develop have been merged into branch 'develop'
    - Any resolved merge conflicts have been pushed back to origin
    - You are now on branch 'develop'

..

If a branch existed on the remote repo (e.g., the ``feature/eliot`` branch used
in testing), it would be deleted:

.. code-block:: none
   :emphasize-lines: 1,5,7,19

   $ git branch -a
   * develop
     master
     remotes/origin/develop
     remotes/origin/feature/eliot
     remotes/origin/master
   $ git hf update
   Fetching origin
   From git.devops.develop:/opt/git/dims-asbuilt
    x [deleted]         (none)     -> origin/feature/eliot

   Summary of actions:
   - Any changes to branches at origin have been downloaded to your local repository
   - Any branches that have been deleted at origin have also been deleted from your local repository
   - Any changes from origin/master have been merged into branch 'master'
   - Any changes from origin/develop have been merged into branch 'develop'
   - Any resolved merge conflicts have been pushed back to origin
   - You are now on branch 'develop'
   $ git branch -a
   * develop
     master
     remotes/origin/develop
     remotes/origin/master

..

While using ``git hf update && git hf pull`` seems like it is simple enough,
the DIMS project has several dozen repos, many of which are inter-related.
Keeping them all up to date is not simple, and because of this developers
often get far out of sync with the rest of the team.

.. _updatingwithmr:

Updating using the ``mr`` command
"""""""""""""""""""""""""""""""""

A useful tool for managing multiple Git repositories and keeping them in sync
with the master branches is to use the program `mr`_.

.. _mr: http://joeyh.name/code/mr/

``mr`` uses a configuration file that can be added to using ``mr register``
within a repo, or by editing/writing the ``.mrconfig`` file directly.

.. attention::

    These instructions assume the reader is *not already using* ``mr``
    on a regular basis. Additionally, all DIMS Git repos are assumed
    to be segrated into their own directory tree apart from any other
    Git repos that the developer may be using.

    This assumption allows for use of a ``.mrconfig`` file specifically for
    just DIMS source code that can be over-written entirely with DIMS-specific
    settings.

    .. todo::

       A script will be written to allow users to more easily do these
       steps. See Jira ticket `DIMS-350`.

    ..

..

.. _DIMS-350: http://jira.devops.develop/browse/DIMS-350

Cloning all of the DIMS source repos at once, or getting the contents of
what should be an up-to-date ``.mrconfig`` file, is covered in the Section
:ref:`cloningmultiplerepos`.

After all repos have been cloned, they can be kept up to date on a daily
basis. Start your work session with the following commands:

.. code-block:: none

    $ cd $GIT
    $ mr update

..

.. caution::

    If you do not update a repo before attempting to ``git hf push`` or
    ``git hf update`` with commited changes, Git will do a ``pull``
    and potentially you will end up with at best a ``merge``, and at
    worst a merge conflict that you must resolve before the ``push`` can
    complete. If you are not comfortable handling a merge conflict, talk
    to another team member to get help.

..

.. _dimsgitsyncrepos:

Updating with ``dims.git.syncrepos``
""""""""""""""""""""""""""""""""""""

A script that combines several of the above steps into one single command
is ``dims.git.synrepos``.

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~ () $ dims.git.syncrepos --version
    dims.git.syncrepos version 1.6.97

..

In the example here, highlighted lines show
where repos are *dirty* (Repo[9], Repo[13], and Repo[33]), meaning they have
tracked files that are not committed yet and cannot be updated, *clean* and
requiring updates from the remote repo (Repo[12]), and new repositories from
the remote server (Repo[28] and Repo[30]) that are being cloned and initialized
for use with `hub-flow` tools. At the end, ``dims.git.syncrepos`` reports
how many repos were updated out of the available repos on the remote
server, how many new repos it added, and/or how many repos could not be
updated because they are dirty. Lastly, it reports how long it took (so
you can be aware of how long you have to go get coffee after
starting a sync.)

.. code-block:: none
   :linenos:
   :emphasize-lines: 1,11-15,19-32,33-34,50-73,75-98,101-103,118,125-127

    [dimsenv] dittrich@dimsdemo1:~ () $ dims.git.syncrepos
    [+++] Found 46 available repos at git@git.devops.develop
    [+++] Repo[1] "/home/dittrich/dims/git/ansible-inventory" clean:
    [+++] Repo[2] "/home/dittrich/dims/git/ansible-playbooks" clean:
    [+++] Repo[3] "/home/dittrich/dims/git/cif-client" clean:
    [+++] Repo[4] "/home/dittrich/dims/git/cif-java" clean:
    [+++] Repo[5] "/home/dittrich/dims/git/configs" clean:
    [+++] Repo[6] "/home/dittrich/dims/git/dims" clean:
    [+++] Repo[7] "/home/dittrich/dims/git/dims-ad" clean:
    [+++] Repo[8] "/home/dittrich/dims/git/dims-asbuilt" clean:
    [---] Repo[9] "/home/dittrich/dims/git/dims-ci-utils" is dirty:
    ?? dims/diffs.1
    ?? dims/manifest.dat
    ?? ubuntu-14.04.2/ubuntu-14.04.3-install.dd.bz2
    4bb5516 (feature/dims-406) Merge branch 'develop' into feature/dims-406

    [+++] Repo[10] "/home/dittrich/dims/git/dims-dashboard" clean:
    [+++] Repo[11] "/home/dittrich/dims/git/dims-db-recovery" clean:
    [+++] Repo[12] "/home/dittrich/dims/git/dims-devguide" clean:
    remote: Counting objects: 29, done.
    remote: Compressing objects: 100% (22/22), done.
    remote: Total 22 (delta 13), reused 0 (delta 0)
    Unpacking objects: 100% (22/22), done.
    From git.devops.develop:/opt/git/dims-devguide
       daffa68..4b2462b  develop    -> origin/develop
    Updating daffa68..4b2462b
    Fast-forward
     .bumpversion.cfg                |  2 +-
     docs/source/conf.py             |  4 ++--
     docs/source/deployconfigure.rst |  2 +-
     docs/source/referenceddocs.rst  | 13 +++++++++++++
     4 files changed, 17 insertions(+), 4 deletions(-)
    [---] Repo[13] "/home/dittrich/dims/git/dims-dockerfiles" is dirty:
    8a47fca (HEAD -> develop) Bump version: 1.1.11 → 1.1.12

    [+++] Repo[14] "/home/dittrich/dims/git/dims-dsdd" clean:
    [+++] Repo[15] "/home/dittrich/dims/git/dims-jds" clean:
    [+++] Repo[16] "/home/dittrich/dims/git/dims-keys" clean:
    [+++] Repo[17] "/home/dittrich/dims/git/dims-ocd" clean:
    [+++] Repo[18] "/home/dittrich/dims/git/dims-packer" clean:
    [+++] Repo[19] "/home/dittrich/dims/git/dims-sample-data" clean:
    [+++] Repo[20] "/home/dittrich/dims/git/dims-sr" clean:
    [+++] Repo[21] "/home/dittrich/dims/git/dims-supervisor" clean:
    [+++] Repo[22] "/home/dittrich/dims/git/dims-svd" clean:
    [+++] Repo[23] "/home/dittrich/dims/git/dimssysconfig" clean:
    [+++] Repo[24] "/home/dittrich/dims/git/dims-tp" clean:
    [+++] Repo[25] "/home/dittrich/dims/git/dims-tr" clean:
    [+++] Repo[26] "/home/dittrich/dims/git/dims-vagrant" clean:
    [+++] Repo[27] "/home/dittrich/dims/git/ELK" clean:
    [+++] Adding Repo[28] fuse4j to /home/dittrich/dims/.mrconfig and checking it out.
    mr checkout: /home/dittrich/dims/git/fuse4j
    Cloning into 'fuse4j'...
    remote: Counting objects: 523, done.
    remote: Compressing objects: 100% (240/240), done.
    remote: Total 523 (delta 186), reused 523 (delta 186)
    Receiving objects: 100% (523/523), 180.86 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (186/186), done.
    Checking connectivity... done.
    Using default branch names.

    Which branch should be used for tracking production releases?
       - master
    Branch name for production releases: [master]
    Branch name for "next release" development: [develop]

    How to name your supporting branch prefixes?
    Feature branches? [feature/]
    Release branches? [release/]
    Hotfix branches? [hotfix/]
    Support branches? [support/]
    Version tag prefix? []

    mr checkout: finished (1 ok; 43 skipped)
    [+++] Repo[29] "/home/dittrich/dims/git/ipgrep" clean:
    [+++] Adding Repo[30] java-native-loader to /home/dittrich/dims/.mrconfig and checking it out.
    mr checkout: /home/dittrich/dims/git/java-native-loader
    Cloning into 'java-native-loader'...
    remote: Counting objects: 329, done.
    remote: Compressing objects: 100% (143/143), done.
    remote: Total 329 (delta 62), reused 329 (delta 62)
    Receiving objects: 100% (329/329), 178.36 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (62/62), done.
    Checking connectivity... done.
    Using default branch names.

    Which branch should be used for tracking production releases?
       - master
    Branch name for production releases: [master]
    Branch name for "next release" development: [develop]

    How to name your supporting branch prefixes?
    Feature branches? [feature/]
    Release branches? [release/]
    Hotfix branches? [hotfix/]
    Support branches? [support/]
    Version tag prefix? []

    mr checkout: finished (1 ok; 44 skipped)
    [+++] Repo[31] "/home/dittrich/dims/git/java-stix-v1.1.1" clean:
    [+++] Repo[32] "/home/dittrich/dims/git/mal4s" clean:
    [---] Repo[33] "/home/dittrich/dims/git/MozDef" is dirty:
     M docker/Dockerfile
     M docker/Makefile

    [+++] Repo[34] "/home/dittrich/dims/git/ops-trust-openid" clean:
    [+++] Repo[35] "/home/dittrich/dims/git/ops-trust-portal" clean:
    [+++] Repo[36] "/home/dittrich/dims/git/poster-deck-2014-noflow" clean:
    [+++] Repo[37] "/home/dittrich/dims/git/prisem" clean:
    [+++] Repo[38] "/home/dittrich/dims/git/prisem-replacement" clean:
    [+++] Repo[39] "/home/dittrich/dims/git/pygraph" clean:
    [+++] Repo[40] "/home/dittrich/dims/git/rwfind" clean:
    [---] Repo[41] "/home/dittrich/dims/git/sphinx-autobuild" is clean:
    [+++] Repo[42] "/home/dittrich/dims/git/stix-java" clean:
    [+++] Repo[43] "/home/dittrich/dims/git/ticketing-redis" clean:
    [+++] Repo[44] "/home/dittrich/dims/git/tsk4j" clean:
    [+++] Repo[45] "/home/dittrich/dims/git/tupelo" clean:
    [+++] Repo[46] "/home/dittrich/dims/git/umich-botnets" clean:
    [+++] Updated 40 of 46 available repos.
    [+++] Summary of actions for repos that were updated:
    - Any changes to branches at origin have been downloaded to your local repository
    - Any branches that have been deleted at origin have also been deleted from your local repository
    - Any changes from origin/master have been merged into branch 'master'
    - Any changes from origin/develop have been merged into branch 'develop'
    - Any resolved merge conflicts have been pushed back to origin
    [+++] Added 3 new repos: fuse4j java-native-loader tsk4j
    [+++] Could not update 3 repos: dims-ci-utils dims-dockerfiles MozDef
    [+++] Updating repos took 00:04:12

..

.. _findingchanges:

Finding Changes and Changed Files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When working with lots of branches, especially branches that last for a long
time, it becomes difficult to find changes and changed files (and the commits
that contain them), in order to ensure required changes are present on the
branch you are working on.

Git has a command ``whatchanged`` that assists with this. (See the
`git-whatchanged`_ documentation.)

Say you are on a branch, and running a program that relies on an Ansible
inventory file for obtaining a list of hosts. When the program is run,
it appears to iterate over the `group names`, not host names as you
expect:

.. code-block:: none
   :emphasize-lines: 2,3

    $ test.vagrant.list --status
    production: not_created
    development: not_created

..

The reason for this is a missing ``:children`` modifier on the
names of a group that has sub-groups. You know this bug was
fixed, but which branch (or which commit) contains the fix?
Use ``git whatchanged`` and pass it the name of the file
that has the problem.

.. code-block:: none
   :emphasize-lines: 1,6,8,16,24,32,40

     $ git whatchanged -- inventory/deployment/all
    commit 963b006a7aceee21eb35da41546ae5da7596382e
    Author: Dave Dittrich <dittrich@u.washington.edu>
    Date:   Wed Dec 14 23:07:37 2016 -0800

        Add missing ":children" modifier

    :100644 100644 d9918d0... 9ce596b... M  v2/inventory/deployment/all

    commit 00afbb5bfadc46ef9b5f253a13a6212cb3fca178
    Author: Dave Dittrich <dittrich@u.washington.edu>
    Date:   Sun Dec 11 21:31:04 2016 -0800

        Update and normalize inventory 'all' files with children groups

    :100644 100644 99bb8d9... d9918d0... M  v2/inventory/deployment/all

    commit 777cce71f944650c0ff5cf47723ee6b9f322c987
    Author: Dave Dittrich <dittrich@u.washington.edu>
    Date:   Sat Dec 3 21:33:38 2016 -0800

        Refactor inventory directories to use group vars properly

    :100644 100644 98376da... 99bb8d9... M  v2/inventory/deployment/all

    commit 3cdb37d04c9d8bedb5277ad4cfbeafdec55f69b0
    Author: Dave Dittrich <dittrich@u.washington.edu>
    Date:   Tue Nov 22 20:00:19 2016 -0800

        Add vagrants to local and deployment groups

    :100644 100644 b199ae1... 98376da... M  v2/inventory/deployment/all

    commit 92eec6c03c28824725b9fc0c4560b4fdccfa880e
    Author: Dave Dittrich <dittrich@u.washington.edu>
    Date:   Fri Nov 18 16:53:04 2016 -0800

        Add initial inventory for deployment to get dynamic inventory working

    :000000 100644 0000000... b199ae1... A  v2/inventory/deployment/all

..

If you add the ``--patch`` option, you also see the changes themselves
and can identify the initial problem file as well as the commit
that contains the fix (output edited for brevity):



.. code-block:: none
   :emphasize-lines: 2,15-19,21,39-42
   :linenos:

     $ git whatchanged --patch -- inventory/deployment/all
    commit 963b006a7aceee21eb35da41546ae5da7596382e
    Author: Dave Dittrich <dittrich@u.washington.edu>
    Date:   Wed Dec 14 23:07:37 2016 -0800

        Add missing ":children" modifier

    diff --git a/v2/inventory/deployment/all b/v2/inventory/ectf/all
    index d9918d0..9ce596b 100644
    --- a/v2/inventory/deployment/all
    +++ b/v2/inventory/deployment/all
    @@ -27,7 +27,7 @@ red.devops.deployment
     yellow.devops.deployment

     # Hosts are Vagrant virtual machines
    -[vagrants]
    +[vagrants:children]
     production
     development

    commit 00afbb5bfadc46ef9b5f253a13a6212cb3fca178
    Author: Dave Dittrich <dittrich@u.washington.edu>
    Date:   Sun Dec 11 21:31:04 2016 -0800

        Update and normalize inventory 'all' files with children groups

    diff --git a/v2/inventory/deployment/all b/v2/inventory/ectf/all
    index 99bb8d9..d9918d0 100644
    --- a/v2/inventory/deployment/all
    +++ b/v2/inventory/deployment/all
      . . .
     [manager]
     core-[01:03].devops.deployment

    +[worker]
    +red.devops.deployment
    +yellow.devops.deployment
    +
     # Hosts are Vagrant virtual machines
     [vagrants]
    +production
    +development
    +
    +[production]
     red.devops.deployment
     core-[01:03].devops.deployment
     yellow.devops.deployment
    -blue16.devops.deployment
    +
    +[development]
     blue14.devops.deployment
    -green.devops.deployment
     . . .

..

You can now ``git cherry-pick`` the commit with the fix
(``963b006a7aceee21eb35da41546ae5da7596382e``) and move on:

.. code-block:: none
   :emphasize-lines: 2-7

    $ test.vagrant.list --status
    red: saved
    core-01: not_created
    core-02: not_created
    core-03: not_created
    yellow: saved
    blue14: not_created

..

.. _git-whatchanged: https://git-scm.com/docs/git-whatchanged


.. _versionnumbers:

Managing Version Numbers
~~~~~~~~~~~~~~~~~~~~~~~~

The DIMS project uses the Python program `bumpversion`_ to
update version numbers in Git repositories, following
`PEP 440 -- Version Identification and Dependency Specification`_.
You can learn how `bumpversion`_ works from these resources:

   + `bumpversion screencast`_ showing bumpversion in action
   + `A Python Versioning Workflow With Bumpversion`_

.. note::

    You can find examples of using `bumpversion`_ (including its configuration file
    ``.bumpversion.cfg`` and how it is used to manage version numbers in files) in
    this document in Section :ref:`cherrypickingcommits`.
..

The program ``bumpversion`` is included in the Python virtual environment
``dimsenv`` that is created for use in DIMS development.

.. todo::

    Add a cross reference to description of the ``dimsenv``
    Python Virtual Environment.

..

.. code-block:: none

    [dimsenv] dittrich@27b:~/git/homepage (develop*) $ which bumpversion
    /Users/dittrich/dims/envs/dimsenv/bin/bumpversion

..

.. caution::

    Because you must be in the same directory as the ``.bumpversion.cfg`` file
    when you invoke ``bumpversion``, it is sometimes problematic when using it
    to work in a sub-directory one or more levels below the configuration file.
    You may see example command lines like ``(cd ..; bumpversion patch)`` that
    use sub-shells to temporarily change to the right directory, do the
    ``bumpversion patch``, then exit leaving you in the same directory where you
    are editing files.  That is a little more work than is desirable, but
    doing a bunch of ``cd ..``, ``bumpersion patch``, ``cd backagain``
    is even more work.

..

To make it easier to increment version numbers, a helper
script ``dims.bumpversion`` is available as well:

.. code-block:: none

    [dimsenv] dittrich@27b:~/git/homepage (develop*) $ which dims.bumpversion
    /Users/dittrich/dims/envs/dimsenv/bin/dims.bumpversion
    [dimsenv] dittrich@27b:~/git/homepage (develop*) $ dims.bumpversion --help
    Usage:
    /Users/dittrich/dims/envs/dimsenv/bin/dims.bumpversion [options] [args]

    Use "/Users/dittrich/dims/envs/dimsenv/bin/dims.bumpversion --help" to see options.
    Use "/Users/dittrich/dims/envs/dimsenv/bin/dims.bumpversion --usage" to see help on "bumpversion" itself.

    /Users/dittrich/dims/envs/dimsenv/bin/dims.bumpversion -- [bumpversion_options] [bumpversion_args]

    Follow this second usage example and put -- before any bumpversion
    options and arguments to pass them on bumpversion (rather than
    process them as though they were /Users/dittrich/dims/envs/dimsenv/bin/dims.bumpversion arguments.) After
    all, /Users/dittrich/dims/envs/dimsenv/bin/dims.bumpversion is just a shell wrapping bumpversion.


    Options:
      -h, --help     show this help message and exit
      -d, --debug    Enable debugging
      -u, --usage    Print usage information.
      -v, --verbose  Be verbose (on stdout) about what is happening.

..

The default when you just invoke ``dims.bumpversion`` is to do ``bumpversion patch``,
the most frequent version increment. To use a different increment, just add it as
an argument on the command line (e.g., ``dims.bumpvesion minor``).

Here is an example of how this section edit was done, showing
the version number increment in the workflow:

.. code-block:: none
   :emphasize-lines: 7

    [dimsenv] dittrich@localhost:~/dims/git/dims-devguide/docs (develop*) $ git add source/sourcemanagement.rst
    [dimsenv] dittrich@localhost:~/dims/git/dims-devguide/docs (develop*) $ git stat
    M  docs/source/sourcemanagement.rst
    [dimsenv] dittrich@localhost:~/dims/git/dims-devguide/docs (develop*) $ git commit -m "Add subsection on version numbers and bumpversion/dims.bumpversion"
    [develop b433bae] Add subsection on version numbers and bumpversion/dims.bumpversion
     1 file changed, 92 insertions(+)
    [dimsenv] dittrich@localhost:~/dims/git/dims-devguide/docs (develop*) $ dims.bumpversion
    [dimsenv] dittrich@localhost:~/dims/git/dims-devguide/docs (develop*) $ git hf push
    Fetching origin
    Already up-to-date.
    Counting objects: 11, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (11/11), done.
    Writing objects: 100% (11/11), 2.53 KiB | 0 bytes/s, done.
    Total 11 (delta 7), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Oct 22 22:31:50 PDT 2015
    remote: [+++] post-receive-06jenkinsalldocs started
    remote: [+++] REPONAME=dims-devguide
    remote: [+++] BRANCH=develop
    remote: [+++] newrev=00727d53dbc8130cdbdbe35be80f1f4c2d2ee7fa
    remote: [+++] oldrev=e8e7d4db40dd852a044525fdfbada1fe80d81739
    remote: [+++] Branch was updated.
    remote: [+++] This repo has a documentation directory.
    remote:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
    remote:                                  Dload  Upload   Total   Spent    Left  Speed
    remote: 100    79    0     0  100    79      0   2613 --:--:-- --:--:-- --:--:--  3761
    remote:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
    remote:                                  Dload  Upload   Total   Spent    Left  Speed
    remote: 100    78    0     0  100    78      0   2524 --:--:-- --:--:-- --:--:--  3250
    remote: [+++] post-receive-06jenkinsalldocs finished
    To git@git.devops.develop:/opt/git/dims-devguide.git
       e8e7d4d..00727d5  develop -> develop

    Summary of actions:
    - The remote branch 'origin/develop' was updated with your changes

..

.. _bumpversion: https://github.com/peritus/bumpversion
.. _bumpversion screencast: https://asciinema.org/a/3828
.. _A Python Versioning Workflow With Bumpversion: http://kylepurdon.com/blog/2015/01/25/a-python-versioning-workflow-with-bumpversion/
.. _PEP 440 -- Version Identification and Dependency Specification: http://legacy.python.org/dev/peps/pep-0440/

.. _initializingforhubflow:

Initializing a repo for ``hub-flow``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Every time you clone a new DIMS repo, it must be initialized with ``hub-flow``
so that ``hub-flow`` commands work properly.  Initialize your repo this way:

.. code-block:: none
   :emphasize-lines: 1,9,10

    $ git clone git@git.devops.develop:/opt/git/dims-ad.git
    Cloning into 'dims-ad'...
    remote: Counting objects: 236, done.
    remote: Compressing objects: 100% (155/155), done.
    remote: Total 236 (delta 117), reused 159 (delta 76)
    Receiving objects: 100% (236/236), 26.20 MiB | 5.89 MiB/s, done.
    Resolving deltas: 100% (117/117), done.
    Checking connectivity... done.
    $ cd dims-ad
    $ git hf init
    Using default branch names.

    Which branch should be used for tracking production releases?
       - master
    Branch name for production releases: [master]
    Branch name for "next release" development: [develop]

    How to name your supporting branch prefixes?
    Feature branches? [feature/]
    Release branches? [release/]
    Hotfix branches? [hotfix/]
    Support branches? [support/]
    Version tag prefix? []

..

After initializing ``hub-flow``, there will be two new sections
in your ``.git/config`` file starting with ``hubflow``:

.. code-block:: none
   :emphasize-lines: 16-18, 23-28

   $ cat .git/config
   [core]
           repositoryformatversion = 0
           filemode = true
           bare = false
           logallrefupdates = true
           ignorecase = true
           precomposeunicode = true
   [remote "origin"]
           url = git@git.devops.develop:/opt/git/dims-ad.git
           fetch = +refs/heads/*:refs/remotes/origin/*
   [branch "master"]
           remote = origin
           merge = refs/heads/master
           rebase = true
   [hubflow "branch"]
           master = master
           develop = develop
   [branch "develop"]
           remote = origin
           merge = refs/heads/develop
           rebase = true
   [hubflow "prefix"]
           feature = feature/
           release = release/
           hotfix = hotfix/
           support = support/
           versiontag =

..

.. note::

    A possible test for inclusion in the ``dims-ci-utils`` test suite would be
    to check for the existance of the ``hubflow "branch"`` and ``hubflow
    "prefix"`` sections.

    These are automatically created when repos are checked out using the
    ``dims.git.syncrepos`` script and/or methods involving ``mr`` described
    in the following sections.

..

.. _infrequentgittasks:

Infrequent tasks with Git
-------------------------

.. _cloningmultiplerepos:

Cloning multiple repos from ``git.devops.develop``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are several dozen repositories on ``git.devops.develop``
that contain DIMS-generated code, configuration files, and/or documentation,
but also local copies of Git repositories from other sources (some with
DIMS-related customizations).

To get a list of all repositories on ``git.devops.develop``,
use the Git shell command ``list``:

.. code-block:: none
   :emphasize-lines: 1

    $ ssh git@git.devops.develop list
    prisem-replacement.git
    ELK.git
    cif-java.git
    cif-client.git
    dims-ad.git
    supervisor.git
    dims-tr.git
    lemonldap-ng.git
    pygraph.git
    parsons-docker.git
    configs.git
    poster-deck-2014-noflow.git
    dims-keys.git
    dims.git
    dims-tp.git
    ops-trust-portal.git
    dimssysconfig.git
    dims-dockerfiles.git
    stix-java.git
    ansible-playbooks.git
    dims-dashboard.git
    mal4s.git
    dims-ocd.git
    sphinx-autobuild.git
    dims-devguide.git
    dims-asbuilt.git
    ticketing-redis.git
    dims-sr.git
    prisem.git
    umich-botnets.git
    dims-dsdd.git
    dims-sample-data.git
    packer.git
    java-stix-v1.1.1.git
    vagrant.git
    dims-jds.git
    ansible-inventory.git
    ops-trust-openid.git
    dims-coreos-vagrant.git
    configstest.git
    poster-deck-2014.git
    rwfind.git
    dims-ci-utils.git
    ipgrep.git
    tupelo.git
    dims-opst-portal.git
    lemonldap-dims.git
    MozDef.git
    tsk4j.git
    dims-svd.git

..

To clone all of these repositories in a single step, there is
another Git shell command ``mrconfig`` that returns the contents
of a ``.mrconfig`` file (see ``man mr`` for more information).

.. caution::

   To use a ``.mrconfig`` file in a an arbitrary directory, you
   will need to add the directory path to this file to the ``~/.mrtrust``
   file. In this example, we will clone repos into ``~/dims/git`` by
   placing the ``.mrconfig`` file in the ``~/dims`` directory.

   .. code-block:: none

       $ cat ~/.mrtrust
       /Users/dittrich/dims/.mrconfig
       /Users/dittrich/git/.mrconfig

   ..

..

If you are building a documentation set (i.e., a limited set of documentation-only
repositories that are cross-linked using the ``intersphinx`` extension to Sphinx
as described in Section :ref:`intersphinxlinking`.


.. code-block:: none
   :emphasize-lines: 1,2,29,30,98

    $ cd ~/dims
    $ ssh git@git.devops.develop mrconfig dims-ad dims-sr dims-ocd
    [git/dims-ad]
    checkout = git clone 'git@git.devops.develop:/opt/git/dims-ad.git' 'dims-ad' &&
            (cd dims-ad; git hf init)
    show = git remote show origin
    update = git hf update
    pull = git hf update &&
            git hf pull
    stat = git status -s

    [git/dims-sr]
    checkout = git clone 'git@git.devops.develop:/opt/git/dims-sr.git' 'dims-sr' &&
            (cd dims-sr; git hf init)
    show = git remote show origin
    update = git hf update
    pull = git hf update &&
            git hf pull
    stat = git status -s

    [git/dims-ocd]
    checkout = git clone 'git@git.devops.develop:/opt/git/dims-ocd.git' 'dims-ocd' &&
            (cd dims-ocd; git hf init)
    show = git remote show origin
    update = git hf update
    pull = git hf update &&
            git hf pull
    stat = git status -s
    $ ssh git@git.devops.develop mrconfig dims-ad dims-sr dims-ocd > .mrconfig
    $ mr checkout
    mr checkout: /Users/dittrich/dims/git/dims-ad
    Cloning into 'dims-ad'...
    remote: Counting objects: 518, done.
    remote: Compressing objects: 100% (437/437), done.
    remote: Total 518 (delta 308), reused 155 (delta 76)
    Receiving objects: 100% (518/518), 27.88 MiB | 5.88 MiB/s, done.
    Resolving deltas: 100% (308/308), done.
    Checking connectivity... done.
    Using default branch names.

    Which branch should be used for tracking production releases?
       - master
    Branch name for production releases: [master]
    Branch name for "next release" development: [develop]

    How to name your supporting branch prefixes?
    Feature branches? [feature/]
    Release branches? [release/]
    Hotfix branches? [hotfix/]
    Support branches? [support/]
    Version tag prefix? []

    mr checkout: /Users/dittrich/dims/git/dims-ocd
    Cloning into 'dims-ocd'...
    remote: Counting objects: 474, done.
    remote: Compressing objects: 100% (472/472), done.
    remote: Total 474 (delta 288), reused 0 (delta 0)
    Receiving objects: 100% (474/474), 14.51 MiB | 4.26 MiB/s, done.
    Resolving deltas: 100% (288/288), done.
    Checking connectivity... done.
    Using default branch names.

    Which branch should be used for tracking production releases?
       - master
    Branch name for production releases: [master]
    Branch name for "next release" development: [develop]

    How to name your supporting branch prefixes?
    Feature branches? [feature/]
    Release branches? [release/]
    Hotfix branches? [hotfix/]
    Support branches? [support/]
    Version tag prefix? []

    mr checkout: /Users/dittrich/dims/git/dims-sr
    Cloning into 'dims-sr'...
    remote: Counting objects: 450, done.
    remote: Compressing objects: 100% (445/445), done.
    remote: Total 450 (delta 285), reused 0 (delta 0)
    Receiving objects: 100% (450/450), 498.20 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (285/285), done.
    Checking connectivity... done.
    Using default branch names.

    Which branch should be used for tracking production releases?
       - master
    Branch name for production releases: [master]
    Branch name for "next release" development: [develop]

    How to name your supporting branch prefixes?
    Feature branches? [feature/]
    Release branches? [release/]
    Hotfix branches? [hotfix/]
    Support branches? [support/]
    Version tag prefix? []

    mr checkout: finished (3 ok)
    $ mr stat
    mr stat: /Users/dittrich/tmp/dims/git/dims-ad

    mr stat: /Users/dittrich/tmp/dims/git/dims-ocd

    mr stat: /Users/dittrich/tmp/dims/git/dims-sr

    mr stat: finished (3 ok)

..

.. note::

   The example just shown uses only three repos. If you do not specify
   any repo names on the ``mrconfig`` Git shell command, it will return
   the settings for all 50+ DIMS repos. You can then clone the entire
   set of DIMS repositories with the same ``mr checkout`` command,
   and update all of them at once with ``mr update``.

..

Adding a newly-created repository
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Until the ``dims.git.syncrepos`` script has a new feature added to it
to detect when a new repo exists on ``git.devops.develop`` that
does not have a local repo associated with it, you must do this yourself.

When someone uses the ``newrepo`` script to create a new repo on
``git.devops.develop``, you will need to get new ``.mrconfig``
settings for that repo in order for ``dims.git.syncrepo`` to synchronize it.
If you have your ``$GIT`` environment variable pointing to a directory
that *only* has DIMS Git repos in it, you just need to create an updated
``.mrconfig`` file.

.. note::

    It is safest to get a new copy of the ``.mrconfig`` file contents
    and save them to a temporary file that you can compare with the
    current file to ensure you are getting just what you expect, and
    only then over-writing the ``.mrconfig`` file with the new contents.
    The steps are shown here:

..

.. code-block:: none

   $ cd $GIT/..
   $ ssh git@git.devops.develop mrconfig > .mrconfig.new
   $ diff .mrconfig .mrconfig.new
   324a325,333
   > [git/dims-db-recovery]
   > checkout = git clone 'git@git.devops.develop:/opt/git/dims-db-recovery.git' 'dims-db-recovery' &&
   >    (cd dims-db-recovery; git hf init)
   > show = git remote show origin
   > update = git hf update
   > pull = git hf update &&
   >    git hf pull
   > stat = git status -s
   >
   $ mv .mrconfig.new .mrconfig
   $ mr checkout
   mr checkout: /Users/dittrich/dims/git/dims-db-recovery
   Cloning into 'dims-db-recovery'...
   remote: Counting objects: 351, done.
   remote: Compressing objects: 100% (254/254), done.
   remote: Total 351 (delta 63), reused 350 (delta 63)
   Receiving objects: 100% (351/351), 7.60 MiB | 5.62 MiB/s, done.
   Resolving deltas: 100% (63/63), done.
   Checking connectivity... done.
   Using default branch names.

   Which branch should be used for tracking production releases?
      - master
   Branch name for production releases: [master]
   Branch name for "next release" development: [develop]

   How to name your supporting branch prefixes?
   Feature branches? [feature/]
   Release branches? [release/]
   Hotfix branches? [hotfix/]
   Support branches? [support/]
   Version tag prefix? []

   mr checkout: finished (1 ok; 43 skipped)

..


.. _creatinggitrepos:

Creating Git repositories
~~~~~~~~~~~~~~~~~~~~~~~~~

As discussed in the introduction to this section, DIMS software
will be hosted on both a local server ``git.devops.develop``
and from `github.com/uw-dims`_.  This section covers creation of
new repositories on both systems.

.. _creatingreposongithub:

Creating repositories on GitHub
"""""""""""""""""""""""""""""""

.. todo::

    .. note::

        This section is not complete.

    ..

..

.. _settingupremotedimsrepos:

Setting up remote Git repositories on ``git.devops.develop``
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Before a repository can be shared between DIMS team members, a remote
repository must be set up on ``git.devops.develop`` for sharing.
The following is an example session creating a new repository named
``dims-ocd`` for *operational concept description* (a.k.a., *Concept of
Operations*).

.. code-block:: none
   :emphasize-lines: 1,5,7,8,10,11

    $ slogin git.devops.develop
    Welcome to Ubuntu 12.04.5 LTS (GNU/Linux 3.13.0-43-generic x86_64)
    [ ... ]
    Last login: Sun Jan 11 12:04:36 2015 from lancaster.devops.develop
    dittrich@jira:~$ sudo su - gituser
    [sudo] password for dittrich:
    git@jira:~$ cd /opt/git
    git@jira:/opt/git$ newrepo dims-ocd.git
    Initialized empty Git repository in /opt/git/dims-ocd.git/
    git@jira:/opt/git$ echo "DIMS Operational Concept Description" > dims-ocd.git/description
    git@jira:/opt/git$ tree dims-ocd.git
    dims-ocd.git
    ├── branches
    ├── config
    ├── description
    ├── HEAD
    ├── hooks
    │   ├── post-receive -> /opt/git/bin/post-receive
    │   ├── post-receive-00logamqp -> /opt/git/bin/post-receive-00logamqp
    │   └── post-receive-01email -> /opt/git/bin/post-receive-01email
    ├── info
    │   └── exclude
    ├── objects
    │   ├── info
    │   └── pack
    └── refs
        ├── heads
        └── tags

    9 directories, 7 files

..

As can be seen in the output of ``tree`` at the end, the steps above
only create ``post-receive`` hooks for logging to AMQP and sending
email when a ``git push`` is done. To add a Jenkins build hook, do
the following command as well:

.. code-block:: none
   :emphasize-lines: 1,2

    git@jira:/opt/git$ ln -s /opt/git/bin/post-receive-02jenkins dims-ocd.git/hooks/post-receive-02jenkins
    git@jira:/opt/git$ tree dims-ocd.git/hooks/
    dims-ocd.git/hooks/
    ├── post-receive -> /opt/git/bin/post-receive
    ├── post-receive-00logamqp -> /opt/git/bin/post-receive-00logamqp
    ├── post-receive-01email -> /opt/git/bin/post-receive-01email
    └── post-receive-02jenkins -> /opt/git/bin/post-receive-02jenkins

    0 directories, 4 files

..

.. todo::

    .. note::

        The ``newrepo`` command needs to be extended to support
        command line options for selecting hooks, and added to ``list``
        and ``mrconfig`` as Git shell commands for remote access.
        This will greatly simply repo creation by eliminating several
        manual steps that are not easy to remember.

    ..

..

.. _settinguplocalrepo:

Setting up a local Git repository before pushing to remote
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

After setting up the remote repository, you should create the
initial local repository. The basic steps are as follows:

#. Create the new local repo directory.
#. Populate the directory with the files you want in the repo.
#. Add them to the repo.
#. Commit the files with a comment
#. Create an initial version tag.
#. Set ``remote.origin.url`` to point to the remote repo.
#. Push the new repo to the remote repo.
#. Push the tags to the remote repo.

Here is an edited transcript of performing the above tasks.

.. code-block:: none

    $ cd $GIT
    $ mkdir dims-ocd
    $ git init
    Initialized empty Git repository in /Users/dittrich/git/.git/
    [ ... prepare files ... ]
    $ ls
    MIL-STD-498-templates.pdf   UW-logo.png                     conf.py                         newsystem.rst
    Makefile                    _build                          currentsystem.rst               notes.rst
    OCD-DID.pdf                 _static                         impacts.rst                     operationalscenarios.rst
    OCD.html                    _templates                      index.rst                       referenceddocs.rst
    OCD.rst                     analysis.rst                    justifications.rst              scope.rst
    UW-logo-32x32.ico           appendices.rst                  license.txt
    $ rm OCD.rst
    $ ls
    MIL-STD-498-templates.pdf   _build                          currentsystem.rst               notes.rst
    Makefile                    _static                         impacts.rst                     operationalscenarios.rst
    OCD-DID.pdf                 _templates                      index.rst                       referenceddocs.rst
    OCD.html                    analysis.rst                    justifications.rst              scope.rst
    UW-logo-32x32.ico           appendices.rst                  license.txt
    UW-logo.png                 conf.py                         newsystem.rst
    $ git add .
    $ git commit -m "Initial load of MIL-STD-498 template"
    [master (root-commit) 39816fa] Initial load of MIL-STD-498 template
     22 files changed, 1119 insertions(+)
     create mode 100644 dims-ocd/MIL-STD-498-templates.pdf
     create mode 100644 dims-ocd/Makefile
     create mode 100644 dims-ocd/OCD-DID.pdf
     create mode 100755 dims-ocd/OCD.html
     create mode 100644 dims-ocd/UW-logo-32x32.ico
     create mode 100644 dims-ocd/UW-logo.png
     create mode 100644 dims-ocd/_build/.gitignore
     create mode 100644 dims-ocd/_static/.gitignore
     create mode 100644 dims-ocd/_templates/.gitignore
     create mode 100644 dims-ocd/analysis.rst
     create mode 100644 dims-ocd/appendices.rst
     create mode 100644 dims-ocd/conf.py
     create mode 100644 dims-ocd/currentsystem.rst
     create mode 100644 dims-ocd/impacts.rst
     create mode 100644 dims-ocd/index.rst
     create mode 100644 dims-ocd/justifications.rst
     create mode 100644 dims-ocd/license.txt
     create mode 100644 dims-ocd/newsystem.rst
     create mode 100644 dims-ocd/notes.rst
     create mode 100644 dims-ocd/operationalscenarios.rst
     create mode 100644 dims-ocd/referenceddocs.rst
     create mode 100644 dims-ocd/scope.rst
    $ git tag -a "2.0.0" -m "Initial template release"
    $ git remote add origin git@git.devops.develop:/opt/git/dims-ocd.git
    $ git push -u origin master
    Counting objects: 24, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (22/22), done.
    Writing objects: 100% (24/24), 251.34 KiB | 0 bytes/s, done.
    Total 24 (delta 1), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Jan 15 20:46:33 PST 2015
    To git@git.devops.develop:/opt/git/dims-ocd.git
     * [new branch]      master -> master
    Branch master set up to track remote branch master from origin by rebasing.
    $ git push origin --tags
    Counting objects: 1, done.
    Writing objects: 100% (1/1), 173 bytes | 0 bytes/s, done.
    Total 1 (delta 0), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Jan 15 20:46:45 PST 2015
    To git@git.devops.develop:/opt/git/dims-ocd.git
     * [new tag]         2.0.0 -> 2.0.0

..

.. _cherrypickingcommits:

Cherry-picking a commit from one branch to another
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are times when you are working on one branch (e.g., ``feature/coreos``)
and find that there is a bug due to a missing file. This file should be
on the ``develop`` branch from which this feature branch was forked, so
the solution is to fix the bug on the ``develop`` branch and also get
the fix on the feature branch.

As long as that change (e.g., an added file that does not exist on the branch)
has no chance of a conflict, a simple ``cherry-pick`` of the commit will get
things synchronized. Here is an example of the steps:

Let's say the bug was discovered by noticing this error message shows up when
rendering a Sphinx document using ``sphinx-autobuild``:

.. code-block:: none

   +--------- source/index.rst changed ---------------------------------------------
   /Users/dittrich/git/dims-ci-utils/docs/source/lifecycle.rst:306: WARNING: External Graphviz file u'/Users/dittrich/git/dims-ci-utils/Makefile.dot' not found or reading it failed
   +--------------------------------------------------------------------------------

..

The file ``Makefile.dot`` is not found.  (The reason is that the
``lifecycle.rst`` file was moved from a different place, but the
file it included was not.)  We first stash our work (if necessary)
and check out the develop branch. Next, locate the missing file:

.. code-block:: none
   :emphasize-lines: 1,4

   $ git checkout develop
   Switched to branch 'develop'
   Your branch is up-to-date with 'origin/develop'.
   $ find ../.. -name 'Makefile.dot'
   ../../packer/Makefile.dot

..

We now copy the file to where we believe it should reside, and
to trigger a new ``sphinx-autobuild``, we touch the file that
includes it:

.. code-block:: none

   $ cp ../../packer/Makefile.dot ..
   $ touch source/lifecycle.rst

..

Switching to the ``sphinx-autobuild`` status window, we see the error
message has gone away.

.. code-block:: none

   +--------- source/lifecycle.rst changed -----------------------------------------
   +--------------------------------------------------------------------------------

   [I 150331 16:40:04 handlers:74] Reload 1 waiters: None
   [I 150331 16:40:04 web:1825] 200 GET /lifecycle.html (127.0.0.1) 0.87ms
   [I 150331 16:40:04 web:1825] 200 GET /_static/css/theme.css (127.0.0.1) 1.87ms
   [I 150331 16:40:04 web:1825] 304 GET /livereload.js (127.0.0.1) 0.50ms
   [I 150331 16:40:04 web:1825] 200 GET /_static/doctools.js (127.0.0.1) 0.43ms
   [I 150331 16:40:04 web:1825] 200 GET /_static/jquery.js (127.0.0.1) 0.67ms
   [I 150331 16:40:04 web:1825] 200 GET /_static/underscore.js (127.0.0.1) 0.48ms
   [I 150331 16:40:04 web:1825] 200 GET /_static/js/theme.js (127.0.0.1) 0.40ms
   [I 150331 16:40:04 web:1825] 200 GET /_images/virtual_machine_lifecycle_v2.jpeg (127.0.0.1) 4.61ms
   [I 150331 16:40:04 web:1825] 200 GET /_images/whiteboard-lifecycle.png (127.0.0.1) 2.02ms
   [I 150331 16:40:04 web:1825] 200 GET /_images/packer_diagram.png (127.0.0.1) 1.65ms
   [I 150331 16:40:04 web:1825] 200 GET /_images/screenshot-lifecycle.png (127.0.0.1) 1.37ms
   [I 150331 16:40:04 web:1825] 200 GET /_images/vm_org_chart.png (127.0.0.1) 0.70ms
   [I 150331 16:40:04 web:1825] 200 GET /_images/graphviz-f8dca63773d709e39ae45240fc6b7ed94229eb74.png (127.0.0.1) 0.92ms
   [I 150331 16:40:04 web:1825] 200 GET /_static/fonts/fontawesome-webfont.woff?v=4.0.3 (127.0.0.1) 0.55ms
   [I 150331 16:40:05 handlers:109] Browser Connected: http://127.0.0.1:41013/lifecycle.html

..

Now we double-check to make sure we have the change
we expect, add, and commit the fix:

.. code-block:: none
   :emphasize-lines: 1,3,4

   $ git stat
   ?? Makefile.dot
   $ git add ../Makefile.dot
   $ git commit -m "Add Makefile.dot from packer repo for lifecycle.rst"
   [develop d5a948e] Add Makefile.dot from packer repo for lifecycle.rst
    1 file changed, 83 insertions(+)
    create mode 100644 Makefile.dot

..

Make note of the commit that includes just the new file: commit ``d5a948e``
in this case. Now you could bump the version if necessary before pushing.

.. code-block:: none
   :emphasize-lines: 1,2

   $ (cd ..; bumpversion patch)
   $ git hf push
   Fetching origin
   Already up-to-date.
   Counting objects: 10, done.
   Delta compression using up to 8 threads.
   Compressing objects: 100% (10/10), done.
   Writing objects: 100% (10/10), 783 bytes | 0 bytes/s, done.
   Total 10 (delta 8), reused 0 (delta 0)
   remote: Running post-receive hook: Tue Mar 31 17:02:43 PDT 2015
   remote: Scheduled polling of dims-ci-utils-deploy-develop
   remote: Scheduled polling of dims-ci-utils-deploy-master
   remote: Scheduled polling of dims-seed-jobs
   remote: No git consumers for URI git@git.devops.develop:/opt/git/dims-ci-utils.git
   remote: [+++] post-receive-06jenkinsalldocs started
   remote: [+++] REPONAME=dims-ci-utils
   remote: [+++] BRANCH=develop
   remote: [+++] newrev=a95c9e1356ff7c6aaed5bcdbe7b533ffc74b6cc1
   remote: [+++] oldrev=d5a948ebef61da98b7849416ee340e0a4ba45a3a
   remote: [+++] Branch was updated.
   remote: [+++] This repo has a documentation directory.
   remote: [+++] post-receive-06jenkinsalldocs finished
   To git@git.devops.develop:/opt/git/dims-ci-utils.git
      d5a948e..a95c9e1  develop -> develop

   Summary of actions:
   - The remote branch 'origin/develop' was updated with your changes

..

Now you can go back to the feature branch you were working on,
and ``cherry-pick`` the commit with the missing file.

.. code-block:: none
   :emphasize-lines: 1,5,10

   $ git checkout feature/coreos
   Switched to branch 'feature/coreos'
   Your branch is ahead of 'origin/feature/coreos' by 1 commit.
     (use "git push" to publish your local commits)
   $ git cherry-pick d5a948e
   [feature/coreos 14dbf59] Add Makefile.dot from packer repo for lifecycle.rst
    Date: Tue Mar 31 16:38:03 2015 -0700
    1 file changed, 83 insertions(+)
    create mode 100644 Makefile.dot
   $ git log
   commit 14dbf59dff5d6fce51c899b32fef87276dbddef7
   Author: Dave Dittrich <dave.dittrich@gmail.com>
   Date:   Tue Mar 31 16:38:03 2015 -0700

       Add Makefile.dot from packer repo for lifecycle.rst
   ...

..

.. note::

   Note that this results in a new commit hash on this branch
   (in this case, ``14dbf59dff5d6fce51c899b32fef87276dbddef7``).

..

.. _syncingupstream:

Synchronizing with an *upstream* repository
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::

   The DIMS project is using forks of several source repositories, some
   for the sake of local customization, and some for adding features
   necessary for DIMS purposes. The `MozDef`_ project is one of these
   (see the :ref:`dimsad:dimsarchitecturedesign` document, Section
   :ref:`dimsad:conceptofexecution`).

..

.. _MozDef: http://mozdef.readthedocs.org/en/latest/


To track another project's Git repository, syncing
it with a fork that you use locally, it is necessary to
do the following:

* `Configuring a remote for a fork`_
* `Syncing a fork`_

.. _Configuring a remote for a fork: https://help.github.com/articles/configuring-a-remote-for-a-fork/
.. _Syncing a fork: https://help.github.com/articles/syncing-a-fork/

    #. Make sure that you have defined `upstream` properly, e.g.,

        .. code-block:: none

            [dimsenv] ~/dims/git/MozDef (master) $ git remote -v
            origin      git@git.devops.develop:/opt/git/MozDef.git (fetch)
            origin      git@git.devops.develop:/opt/git/MozDef.git (push)
            upstream    git@github.com:jeffbryner/MozDef.git (fetch)
            upstream    git@github.com:jeffbryner/MozDef.git (push)

        ..

    #. Fetch the contents of the ``upstream`` remote repository:

        .. code-block:: none

            [dimsenv] ~/dims/git/MozDef (master) $ git fetch upstream
            remote: Counting objects: 6, done.
            remote: Total 6 (delta 2), reused 2 (delta 2), pack-reused 4
            Unpacking objects: 100% (6/6), done.
            From github.com:jeffbryner/MozDef
               700c1be..4575c0f  master     -> upstream/master
             * [new tag]         v1.12      -> v1.12

        ..

    #. Checkout the branch to sync (e.g., ``master``) and then ``merge``
       any changes:

        .. code-block:: none

            [dimsenv] ~/dims/git/MozDef (master) $ git checkout master
            Already on 'master'
            Your branch is up-to-date with 'origin/master'.
            [dimsenv] ~/dims/git/MozDef (master) $ git merge upstream/master
            Merge made by the 'recursive' strategy.
             alerts/unauth_ssh_pyes.conf |  4 ++++
             alerts/unauth_ssh_pyes.py   | 78 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
             2 files changed, 82 insertions(+)
             create mode 100644 alerts/unauth_ssh_pyes.conf
             create mode 100644 alerts/unauth_ssh_pyes.py
            [dimsenv] ~/dims/git/MozDef (master) $ git push origin master
            Counting objects: 8, done.
            Delta compression using up to 8 threads.
            Compressing objects: 100% (8/8), done.
            Writing objects: 100% (8/8), 2.11 KiB | 0 bytes/s, done.
            Total 8 (delta 3), reused 0 (delta 0)
            remote: Running post-receive hook: Thu Sep 17 20:52:14 PDT 2015
            To git@git.devops.develop:/opt/git/MozDef.git
               180484a..766da56  master -> master

        ..

    #. Now push the updated repository to the "local" `remote repository` (i.e,
       ``git.devops.develop`` for the DIMS project):

        .. code-block:: none

            [dimsenv] ~/dims/git/MozDef (master) $ git push origin master
            Counting objects: 8, done.
            Delta compression using up to 8 threads.
            Compressing objects: 100% (8/8), done.
            Writing objects: 100% (8/8), 2.11 KiB | 0 bytes/s, done.
            Total 8 (delta 3), reused 0 (delta 0)
            remote: Running post-receive hook: Thu Sep 17 20:52:14 PDT 2015
            To git@git.devops.develop:/opt/git/MozDef.git
               180484a..766da56  master -> master

        ..

    #. If the `remote` repository is itself the fork (e.g., if you fork a
       repository on GitHub, then want to maintain a "local" `remote repository`
       on-site for your project, you may wish to use a label other than
       ``upstream`` to connote the fork differently):


        .. code-block:: none

            [dimsenv] ~/git/ansible (release1.8.4*) $ git remote -v
            davedittrich        git@github.com:davedittrich/ansible.git (fetch)
            davedittrich        git@github.com:davedittrich/ansible.git (push)
            origin      https://github.com/ansible/ansible.git (fetch)
            origin      https://github.com/ansible/ansible.git (push)

        ..

.. _startingarelease:

Starting a "release"
~~~~~~~~~~~~~~~~~~~~

By convention, DIMS repositories have at least one file, named ``VERSION``,
that contains the release version number. You can see the current release by
looking at the contents of this file.

.. code-block:: none

    $ cat VERSION
    1.1.4

..

.. note::

    There may be other files, such as the Sphinx documentation configuration
    file, ``docs/source/conf.py`` usually, or other source files for Python
    or Java builds. Each of the files that has a version/release number in
    it **must** use the same string and be included in the ``.bumpversion.cfg``
    file in order for ``bumpversion`` to properly manage release numbers.

..

Now that you know what the current version number is, you can initiate
a release branch with ``hub-flow``, knowing that the new numbr will be.
In this case, we will create a release branch ``1.2.0`` to increment
the minor version number component.

.. code-block:: none

    $ git hf release start 1.2.0
    Fetching origin
    Switched to a new branch 'release/1.2.0'
    Total 0 (delta 0), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Jan 22 18:33:54 PST 2015
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
     * [new branch]      release/1.2.0 -> release/1.2.0

    Summary of actions:
    - A new branch 'release/1.2.0' was created, based on 'dev'
    - The branch 'release/1.2.0' has been pushed up to 'origin/release/1.2.0'
    - You are now on branch 'release/1.2.0'

    Follow-up actions:
    - Bump the version number now!
    - Start committing last-minute fixes in preparing your release
    - When done, run:

         git hf release finish '1.2.0'

..

You should now be on the new release branch:

.. code-block:: none

    $

..

After making any textual changes, bump the version number
to match the new release number:

.. code-block:: none

    $ bumpversion minor

..

Now the release can be finished. You will be placed in an editor
to create comments for actions like merges and tags.

.. code-block:: none

    $ bumpversion minor
    $ cat VERSION
    1.2.0
    $ git hf release finish '1.2.0'
    Fetching origin
    Fetching origin
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 690 bytes | 0 bytes/s, done.
    Total 9 (delta 7), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Jan 22 18:37:24 PST 2015
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
       3ac28a2..5ca145b  release/1.2.0 -> release/1.2.0
    Switched to branch 'master'
    Your branch is up-to-date with 'origin/master'.
    Removing roles/tomcat/tasks/main.yml
    Removing roles/tomcat/handlers/main.yml
    Removing roles/tomcat/defaults/main.yml
    Removing roles/postgres/templates/pg_hba.conf.j2
    Removing roles/postgres/files/schema.psql
    Removing roles/ozone/files/postgresql-9.3-1102.jdbc41.jar
    Auto-merging roles/logstash/files/demo.logstash.deleteESDB
    Auto-merging roles/logstash/files/demo.logstash.addwebsense
    Auto-merging roles/logstash/files/demo.logstash.addufw
    Auto-merging roles/logstash/files/demo.logstash.addrpcflow
    Auto-merging roles/logstash/files/demo.logstash.addcymru

    [ ... ]

    ~
    ".git/MERGE_MSG" 7L, 280C written
    Merge made by the 'recursive' strategy.
     .bumpversion.cfg                                                         |   11 +
     Makefile                                                                 |   61 +-
     VERSION                                                                  |    1 +
     configure-all.yml                                                        |    5 +-
     dims-all-desktop.yml                                                     |   56 +
     dims-all-server.yml                                                      |  125 ++
     dims-cifv1-server.yml                                                    |   50 +

    [...]


    Release 1.2.0.
    #
    # Write a message for tag:
    #   1.2.0
    # Lines starting with '#' will be ignored.

    [...]

    ~
    ".git/TAG_EDITMSG" 5L, 97C written
    Switched to branch 'dev'
    Your branch is up-to-date with 'origin/dev'.

    Merge tag '1.2.0' into dev for
    Merge tag '1.2.0' into dev for
    Merge tag '1.2.0' into dev for Release 1.2.0.

    # Please enter a commit message to explain why this merge is necessary,
    # especially if it merges an updated upstream into a topic branch.
    #
    # Lines starting with '#' will be ignored, and an empty message aborts
    # the commit.

    [...]

    ".git/MERGE_MSG" 7L, 273C written
    Merge made by the 'recursive' strategy.
     .bumpversion.cfg    | 2 +-
     VERSION             | 2 +-
     docs/source/conf.py | 4 ++--
     group_vars/all      | 2 +-
     4 files changed, 5 insertions(+), 5 deletions(-)
    Deleted branch release/1.2.0 (was 5ca145b).
    Counting objects: 2, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (2/2), 447 bytes | 0 bytes/s, done.
    Total 2 (delta 0), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Jan 22 18:38:17 PST 2015
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
       3ac28a2..aec921c  dev -> dev
    Total 0 (delta 0), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Jan 22 18:38:19 PST 2015
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
       2afb58f..2482d07  master -> master
    Counting objects: 1, done.
    Writing objects: 100% (1/1), 166 bytes | 0 bytes/s, done.
    Total 1 (delta 0), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Jan 22 18:38:25 PST 2015
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
     * [new tag]         1.2.0 -> 1.2.0
    remote: Running post-receive hook: Thu Jan 22 18:38:28 PST 2015
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
     - [deleted]         release/1.2.0

    Summary of actions:
    - Latest objects have been fetched from 'origin'
    - Release branch has been merged into 'master'
    - The release was tagged '1.2.0'
    - Tag '1.2.0' has been back-merged into 'dev'
    - Branch 'master' has been back-merged into 'dev'
    - Release branch 'release/1.2.0' has been deleted
    - 'dev', 'master' and tags have been pushed to 'origin'
    - Release branch 'release/1.2.0' in 'origin' has been deleted.

..

Lastly, bump the patch version number in the ``dev`` branch to make sure
that when something reports the version in developmental code builds, it
doesn't look like you are using code from the *last tagged* ``master``
branch.  That completely defeats the purpose of using version numbers for
dependency checks or debugging.

.. code-block:: none

    $ bumpversion patch
    $ git push
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 683 bytes | 0 bytes/s, done.
    Total 9 (delta 7), reused 0 (delta 0)
    remote: Running post-receive hook: Thu Jan 22 18:51:00 PST 2015
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
       aec921c..d4fe053  dev -> dev

..

Figure :ref:`newrelease` shows what the branches look like with
GitX.app on a Mac:

.. _newrelease:

.. figure:: images/gitx-newrelease.png
   :alt: New 1.2.0 release, dev on 1.2.1
   :width: 80%
   :align: center

   New 1.2.0 release on master, dev now on 1.2.1.

..

.. _branchrenaming:

Branch Renaming
~~~~~~~~~~~~~~~

Several of the git repos comprising the DIMS source code management
system are using the name ``dev`` for the main development branch.  The
(somewhat) accepted name for the development branch is ``develop``, as detailed
in e.g. `http://nvie.com/posts/a-successful-git-branching-model/`.

We would therefore like to rename any dev branch to develop throughout
our git repo set.  This will of course impact team members who use the
central repos to share work.  Research online suggests that branch
renaming can be done.  The best source found was
https://gist.github.com/lttlrck/9628955, who suggested a three-part
operation

.. code-block:: none

   # Rename branch locally
   git branch -m old_branch new_branch
   # Delete the old branch
   git push origin :old_branch
   # Push the new branch, set local branch to track the new remote
   git push --set-upstream origin new_branch

..

To test this recipe out without impacting any existing repos and
therefore avoiding any possible loss of real work, we constructed a
test situation with a central repo and two fake 'users' who both push
and pull from that repo.  A branch rename is then done, following the
recipe above.  The impact on each of the two users is noted.

First, we create a bare repo.  This will mimic our authoratitive repos
on ``git.devops.develop``.  We'll call this repo ``dims-328.git``, named after the DIMS
Jira ticket created to study the branch rename issue:

.. code-block:: none

   $ cd
   $ mkdir depot
   $ cd depot
   $ git init --bare dims-328.git

..

Next, we clone this repo a first time, which simulates the first
'user' (replace /home/stuart/ with your local path):

.. code-block:: none

   $ cd
   $ mkdir scratch
   $ cd scratch
   $ git clone file:///home/stuart/depot/dims-328.git

..

Next, we dd some content in master branch

.. code-block:: none

   $ cd dims-328
   $ echo content > foo
   $ git add foo
   $ git commit -m "msg"
   $ git push origin master

..

We now clone the 'depot' repo a second time, to simulate the second
user.  Both users are then developing using the authoratitive repo as
the avenue to share work.  Notice how the second user clones into the
specified directory ``dims-328-2``, so as not to tread on the first user's
work:

.. code-block:: none

   $ cd ~/scratch
   $ git clone file:///home/stuart/depot/dims-328.git dims-328-2

..

`user1` (first clone) then creates a ``dev`` branch and adds some content to
it:

.. code-block:: none

   $ cd ~/scratch/dims-328
   $ git branch dev
   $ git checkout dev
   $ echo content > devbranch
   $ git add devbranch
   $ git commit -m "added content to dev branch"
   $ git push origin dev

..

This will create a ``dev`` branch in the origin repo, i.e the depot.

Next, as the second user, pull the changes, checkout ``dev`` and edit:

.. code-block:: none

   $ cd ~scratch/dims-328-2
   $ git pull
   $ git checkout dev
   $ echo foo >> devbranch

..

At this point we have two 'users' with local repos, both of which share
a common upstream repo.  Both users have got the dev branch checked
out, and may have local changes on that branch.


Now, we wish to rename the branch ``dev`` to ``develop`` throughout, i.e. at
the depot and in users' repos.

Using instructions from https://gist.github.com/lttlrck/9628955, and
noting the impacts to each user, we first act as `user1`, who will be
deemed 'in charge' of the renaming process:

.. code-block:: none

   $ cd ~scratch/dims-328
   $ git branch -m dev develop
   $ git push origin :dev
   To file:///home/stuart/depot/dims-328.git
    - [deleted]         dev
   $ git push --set-upstream origin develop
   Counting objects: 2, done.
   Delta compression using up to 8 threads.
   Compressing objects: 100% (2/2), done.
   Writing objects: 100% (2/2), 259 bytes | 0 bytes/s, done.
   Total 2 (delta 0), reused 0 (delta 0)
   To file:///home/stuart/depot/dims-328.git
    * [new branch]      develop -> develop
   Branch develop set up to track remote branch develop from origin.

..

.. warning::

   (This reads like a ..warning block. Is that how it was meant?)

   The ``git push`` output message implies a deletion of the ``dev`` branch in
   the depot.  If `user2` were to interact with ``origin/dev`` now, what would
   happen??

..

Here are the contents of `user1`'s ``.git/config`` after the 3-operation
rename:

.. code-block:: none

   $ cat .git/config
   [core]
       repositoryformatversion = 0
       filemode = true
       bare = false
       logallrefupdates = true
   [remote "origin"]
       url = file:///home/stuart/depot/dims-328.git
       fetch = +refs/heads/*:refs/remotes/origin/*
   [branch "master"]
       remote = origin
       merge = refs/heads/master
   [branch "develop"]
       remote = origin
       merge = refs/heads/develop

..

Note how there are references to ``develop`` but none to ``dev``.  As far as
`user1` is concerned, the branch rename appears to have worked and is complete.

Now, what does `user2` see? With ``dev`` branch checked out, `and` with a
local mod, we do a ``pull``:

.. code-block:: none

   $ cd ~scratch/dims-328-2
   $ git pull
   From file:///home/stuart/depot/dims-328
    * [new branch]      develop    -> origin/develop
   Your configuration specifies to merge with the ref 'dev'
   from the remote, but no such ref was fetched.

..

This is some form of error message.  `user2`'s ``.git/config`` at this
point is this:

.. code-block:: none

   $ cat .git/config
   [core]
       repositoryformatversion = 0
       filemode = true
       bare = false
       logallrefupdates = true
   [remote "origin"]
       url = file:///home/stuart/depot/dims-328.git
       fetch = +refs/heads/*:refs/remotes/origin/*
   [branch "master"]
       remote = origin
       merge = refs/heads/master
   [branch "dev"]
       remote = origin
       merge = refs/heads/dev

..

Perhaps just the branch rename will work for `user2`? As `user2`, we do the
first part of the `rename recipe`:

.. code-block:: none

   $ git branch -m dev develop

..

No errors from this, but `user2`'s ``.git/config`` still refers to a
``dev`` branch:

.. code-block:: none
   :emphasize-lines: 15

   $ cat .git/config
   [core]
       repositoryformatversion = 0
       filemode = true
       bare = false
       logallrefupdates = true
   [remote "origin"]
       url = file:///home/stuart/depot/dims-328.git
       fetch = +refs/heads/*:refs/remotes/origin/*
   [branch "master"]
       remote = origin
       merge = refs/heads/master
   [branch "develop"]
       remote = origin
       merge = refs/heads/dev

..

Next, as `user2`, we issued the third part of the `rename recipe` (but skipped
the second part):

.. code-block:: none

   $ git push --set-upstream origin develop
   Branch develop set up to track remote branch develop from origin.
   Everything up-to-date.

..

Note that this is a ``push``, but since ``user2`` had no committed changes
locally, no content was actually pushed.

.. todo::

    .. note::

        Not yet tested what would occur should that have been the case.

    ..

..

Now `user2`'s ``.git/config`` looks better, the token ``dev`` has changed to
``develop``:

.. code-block:: none
   :emphasize-lines: 15

   $ cat .git/config
   [core]
           repositoryformatversion = 0
           filemode = true
           bare = false
           logallrefupdates = true
   [remote "origin"]
           url = file:///home/stuart/depot/dims-328.git
           fetch = +refs/heads/*:refs/remotes/origin/*
   [branch "master"]
           remote = origin
           merge = refs/heads/master
   [branch "develop"]
           remote = origin
           merge = refs/heads/develop

..

Next, as `user2`, commit the local change, and push to depot:

.. code-block:: none

   $ git add devbranch
   $ git commit -m "msg"
   $ git push

..

So it appears that `user2` can issue just the branch rename and upstream
operation, and skip the second component of the 3-part recipe (``git push
origin :old_branch``), likely since this is an operation on the remote
(depot) itself and was already done by `user1`.

Finally, we switch back to `user1` and pull changes made by `user2`:

.. code-block:: none

   $ cd ~scratch/dims-328
   $ git pull

..

.. warning::

    This has addressed `only` git changes.  The wider implications of a git
    branch rename on systems such as Jenkins has yet to be addressed.  Since
    systems like Jenkins generally just clone or pull from depots, it is
    expected that only git URLs need to change from including ``dev`` to
    ``develop``.

..

.. _deletingtags:

Deleting accidentally created tags
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When trying to finish a release, you may accidentally create a tag
named ``finish``.  It may even get propagated automatically to
``origin``, in which case it could propagate to others' repos:

.. code-block:: none

    mr update: /Users/dittrich/dims/git/dims-keys
    Fetching origin
    From git.devops.develop:/opt/git/dims-keys
     * [new tag]         finish     -> finish

..

You can delete them locally and remotely with the
following commands:

.. code-block:: none
   :emphasize-lines: 1,3

    $ git tag -d finish
    Deleted tag 'finish' (was 516d9d2)
    $ git push origin :refs/tags/finish
    remote: Running post-receive hook: Thu Aug  6 16:07:17 PDT 2015
    To git@git.devops.develop:/opt/git/dims-keys.git
     - [deleted]         finish

..


.. _recoveringdeletedfiles:

Recovering deleted files
~~~~~~~~~~~~~~~~~~~~~~~~

Files that have been deleted in the past, and the deletions commited, can be
recovered by searching the Git history of deletions to identify the commit that
included the deletion. The file can then be checked out using the predecessor
to that commit. See `Find and restore a deleted file in a Git repository`_

.. _Find and restore a deleted file in a Git repository: http://stackoverflow.com/questions/953481/find-and-restore-a-deleted-file-in-a-git-repository


.. _fixingcomments:

Fixing comments in unpublished commits
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::

   This section was derived from http://makandracards.com/makandra/868-change-commit-messages-of-past-git-commits

   .. warning::

       Only do this if you have **not already pushed** the changes!!
       As noted in the ``git-commit`` man page for the ``--amend`` option:

       .. code-block:: none

           You should understand the implications of rewriting history if you
           amend a commit that has already been published. (See the "RECOVERING
           FROM UPSTREAM REBASE" section in git-rebase(1).)

       ..

   ..

..

There may be times when you accidentally make multiple commits,
one at a time, using the same comment (but the changes are
not related to the comment).


Here is an example of three commits all made with ``git commit -am``
using the same message:

.. code-block:: none
   :emphasize-lines: 2,8,14,20,27

    $ git log
    commit 08b888b9dd33f53f0e26d8ff8aab7309765ad0eb
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:35:08 2015 -0700

        Fix intersphinx links to use DOCSURL env variable

    commit 7f3d0d8134c000a787aad83f2690808008ed1d96
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:34:40 2015 -0700

        Fix intersphinx links to use DOCSURL env variable

    commit f6f5d868c8ddd12018ca662a54d1f58c150e6364
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:33:59 2015 -0700

        Fix intersphinx links to use DOCSURL env variable

    commit 96575c967f606e2161033de92dd2dc580ad60a8b
    Merge: 1253ea2 dae5aca
    Author: Linda Parsons <lparsonstech@gmail.com>
    Date:   Thu Apr 30 14:00:49 2015 -0400

        Merge remote-tracking branch 'origin/develop' into develop

    commit 1253ea20bc553759c43d3a999b81be009851d195
    Author: Linda Parsons <lparsonstech@gmail.com>
    Date:   Thu Apr 30 14:00:19 2015 -0400

        Added information for deploying to infrastructure

..

.. note::

   Make note that the commit immediately prior to the three
   erroneously commented commits is ``96575c96``. We will use
   that commit number in a moment...

..

Looking at the patch information shows these are clearly not
all correctly commented:

.. code-block:: none
   :emphasize-lines: 8-26,34-60,68-83

    $ git log --patch
    commit 08b888b9dd33f53f0e26d8ff8aab7309765ad0eb
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:35:08 2015 -0700

        Fix intersphinx links to use DOCSURL env variable

    diff --git a/docs/makedocset b/docs/makedocset
    index dafbedb..9adb954 100644
    --- a/docs/makedocset
    +++ b/docs/makedocset
    @@ -7,7 +7,14 @@
     # This is useful for building a set of documents that employ
     # intersphinx linking, obtaining the links from the co-local
     # repositories instead of specified remote locations.
    +#
    +# To build the docs for a specific server (e.g., when building
    +# using a local docker container running Nginx), set the
    +# environment variable DOCSURL to point to the server:
    +#
    +# $ export DOCSURL=http://192.168.99.100:49153

    +DOCSURL=${DOCSURL:-http://app.devops.develop:8080/docs/devel}

     # Activate dimsenv virtual environment for Sphinx
     . $HOME/dims/envs/dimsenv/bin/activate

    commit 7f3d0d8134c000a787aad83f2690808008ed1d96
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:34:40 2015 -0700

        Fix intersphinx links to use DOCSURL env variable

    diff --git a/docs/source/conf.py b/docs/source/conf.py
    index 9fdc100..b3cd483 100644
    --- a/docs/source/conf.py
    +++ b/docs/source/conf.py
    @@ -351,13 +351,16 @@ epub_exclude_files = ['search.html']
     # If false, no index is generated.
     #epub_use_index = True

    +os.environ['GITBRANCH'] = "develop"
    +
    +if os.environ.get('DOCSURL') is None:
    +    #os.environ['DOCSURL'] = "file://{}".format(os.environ.get('GIT'))
    +    os.environ['DOCSURL'] = "http://app.devops.develop:8080/docs/{}/html/".format(
    +        os.environ['GITBRANCH'])

     intersphinx_cache_limit = -1   # days to keep the cached inventories (0 == forever)
     intersphinx_mapping = {
    -        'dimsocd': ("%s/dims/docs/dims-ocd" % os.environ['HOME'],
    -                    ('http://app.devops.develop:8080/docs/develop/html/dims-ocd/objects.inv', None)),
    -        'dimsad': ("%s/dims/docs/dims-ad" % os.environ['HOME'],
    -                    ('http://app.devops.develop:8080/docs/develop/html/dims-ad/objects.inv', None)),
    -        'dimssr': ("%s/dims/docs/dims-sr" % os.environ['HOME'],
    -                    ('http://app.devops.develop:8080/docs/develop/html/dims-sr/objects.inv', None))
    +        'dimsocd': ("{}/dims-ocd".format(os.environ['DOCSURL']), None),
    +        'dimsad': ("{}/dims-ad".format(os.environ['DOCSURL']), None),
    +        'dimssr': ("{}/dims-sr".format(os.environ['DOCSURL']), None)
     }

    commit f6f5d868c8ddd12018ca662a54d1f58c150e6364
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:33:59 2015 -0700

        Fix intersphinx links to use DOCSURL env variable

    diff --git a/docs/makedocs b/docs/makedocs
    deleted file mode 100644
    index dafbedb..0000000
    --- a/docs/makedocs
    +++ /dev/null
    @@ -1,66 +0,0 @@
    -#!/bin/bash -x
    -#
    -# This script builds multiple Sphinx documents in repos
    -# residing (in their current checkout branch/state) in
    -# the directory specified by the $GIT environment variable.
    -#
    -# This is useful for building a set of documents that employ
    -# intersphinx linking, obtaining the links from the co-local
    -# repositories instead of specified remote locations.
    ...

..


The last commit is easy to fix. Just use ``git commit --amend``
and edit the message:

.. code-block:: none

    $ git commit --amend

    Add DOCSURL selection of where docs reside for intersphinx links

    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.
    #
    # Date:      Thu Apr 30 18:35:08 2015 -0700
    #
    # On branch develop
    # Your branch is ahead of 'origin/develop' by 3 commits.
    #   (use "git push" to publish your local commits)
    #
    # Changes to be committed:
    #       modified:   makedocset

..

Now we can see the message has been changed, but so has the
commit hash!

.. code-block:: none
   :emphasize-lines: 2

    $ git log --patch
    commit 654cb34378cb0a4140725a37e3724b6dcee7aebd
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:35:08 2015 -0700

        Add DOCSURL selection of where docs reside for intersphinx links

    diff --git a/docs/makedocset b/docs/makedocset
    index dafbedb..9adb954 100644
    --- a/docs/makedocset
    +++ b/docs/makedocset
    @@ -7,7 +7,14 @@
     # This is useful for building a set of documents that employ
     # intersphinx linking, obtaining the links from the co-local
     # repositories instead of specified remote locations.
    +#
    +# To build the docs for a specific server (e.g., when building
    +# using a local docker container running Nginx), set the
    +# environment variable DOCSURL to point to the server:
    +#
    +# $ export DOCSURL=http://192.168.99.100:49153

    +DOCSURL=${DOCSURL:-http://app.devops.develop:8080/docs/devel}

     # Activate dimsenv virtual environment for Sphinx
     . $HOME/dims/envs/dimsenv/bin/activate

    commit 7f3d0d8134c000a787aad83f2690808008ed1d96
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:34:40 2015 -0700

        Fix intersphinx links to use DOCSURL env variable

    diff --git a/docs/source/conf.py b/docs/source/conf.py
    ...

..

The second commit has the correct comment, but commit ``f6f5d868c``
was simply renaming a file. It got caught up as a commit when
the ``-a`` option was given when committing the changed file,
not realizing the renamed file had already been added to the
cache.

To change the message for *only* commit ``f6f5d86``, start an interactive
rebase at the commit immediately prior to that commit (in this case,
commit ``96575c9``).  Change ``pick`` to ``edit`` for that commit.

.. code-block:: none
   :emphasize-lines: 3

    $ git rebase -i 96575c9

    edit f6f5d86 Fix intersphinx links to use DOCSURL env variable
    pick 7f3d0d8 Fix intersphinx links to use DOCSURL env variable
    pick 654cb34 Add DOCSURL selection of where docs reside for intersphinx links

    # Rebase 96575c9..654cb34 onto 96575c9 (       3 TODO item(s))
    #
    # Commands:
    # p, pick = use commit
    # r, reword = use commit, but edit the commit message
    # e, edit = use commit, but stop for amending
    # s, squash = use commit, but meld into previous commit
    # f, fixup = like "squash", but discard this commit's log message
    # x, exec = run command (the rest of the line) using shell
    #
    # These lines can be re-ordered; they are executed from top to bottom.
    #
    # If you remove a line here THAT COMMIT WILL BE LOST.
    #
    # However, if you remove everything, the rebase will be aborted.
    #
    # Note that empty commits are commented out

..

As soon as you exit the editor, Git will begin the rebase
and tell you what to do next:

.. code-block:: none

    Stopped at f6f5d868c8ddd12018ca662a54d1f58c150e6364... Fix intersphinx links to use DOCSURL env variable
    You can amend the commit now, with

            git commit --amend

    Once you are satisfied with your changes, run

            git rebase --continue

..

Now use ``git commit --amend`` to edit the comment:

.. code-block:: none

    $ git commit --amend

    Rename makedocs -> makedocset

    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.
    #
    # Date:      Thu Apr 30 18:33:59 2015 -0700
    #
    # rebase in progress; onto 96575c9
    # You are currently editing a commit while rebasing branch 'develop' on '96575c9'.
    #
    # Changes to be committed:
    #       renamed:    makedocs -> makedocset
    #

..

Finish off by continuing the rebase for the remaining commits.

.. code-block:: none

    $ git rebase --continue
    Successfully rebased and updated refs/heads/develop.

..

Now ``git log`` shows the correct comments, as well as
new commit hashes:

.. code-block:: none

    $ git log
    commit 89af6d9fda07276d3cb06dfd2977f1392fb03b25
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:35:08 2015 -0700

        Add DOCSURL selection of where docs reside for intersphinx links

    commit c2c55ff3dcbf10739c5d86ce8a6192e930ccd265
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:34:40 2015 -0700

        Fix intersphinx links to use DOCSURL env variable

    commit 2155936ad7e3ae71ef5775b2036a4b6c21a9a86d
    Author: Dave Dittrich <dave.dittrich@gmail.com>
    Date:   Thu Apr 30 18:33:59 2015 -0700

        Rename makedocs -> makedocset

    commit 96575c967f606e2161033de92dd2dc580ad60a8b
    Merge: 1253ea2 dae5aca
    Author: Linda Parsons <lparsonstech@gmail.com>
    Date:   Thu Apr 30 14:00:49 2015 -0400

        Merge remote-tracking branch 'origin/develop' into develop

..

.. _squashingWithRebase:

Squashing Commits Before Merging
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Working on a feature branch for a long time can mean many changes
are made. The idea of "commit often" to push code so other team
members can review it means that sometimes multiple edits are made
(or reverted commits) while debugging something. Or you may make
a number of changes that are unrelated to the topic of the feature
branch that would be best kept together in a single commit.

It is possible to combine multiple commits into a single commit
using an interactive Git rebase (``git rebase -i``). The idea is
to interactively select a starting point for the rebase operation,
then using ``pick`` and ``squash`` to select the proper commits
to keep, and those subsequent commits that should be merged into
the previous commit. If there are dozens of commits, this can
get very complicated, but the idea can be demonstrated with
three simple changes that we wish to turn into just one
merge commit.

.. note::

    This example is being done with a temporary repository that we
    will make for this purpose, allowing experimentation in a way
    that will not result in harm to an actual repo.

..

Start by initializing a temporary repo in ``/tmp/testing``.

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing () $ git init .
    Initialized empty Git repository in /private/tmp/testing/.git/

..

We now create three files, each with a numeric name and the
corresponding number as the contents of the file, and add
each file to the staging area.

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing () $ for i in 1 2 3; do echo $i > $i.txt; git add $i.txt; done
    [dimsenv] dittrich@ren:/tmp/testing () $ git stat
    A  1.txt
    A  2.txt
    A  3.txt

..

We now make our initial commit.

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing () $ git commit -m 'Initial commit'
    [master (root-commit) 3ee79c4] Initial commit
     3 files changed, 3 insertions(+)
     create mode 100644 1.txt
     create mode 100644 2.txt
     create mode 100644 3.txt

..

Now we check out a branch were we will make our changes, before merging
them back into ``master``.

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (master) $ git checkout -b foo
    Switched to a new branch 'foo'

..

We now make three changes ("edits" to two files, and one file deletion).

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (foo) $ echo "22222" > 2.txt
    [dimsenv] dittrich@ren:/tmp/testing (foo*) $ git add 2.txt
    [dimsenv] dittrich@ren:/tmp/testing (foo*) $ git commit -m "First edit"
    [foo 71738c7] First edit
     1 file changed, 1 insertion(+), 1 deletion(-)

..

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (foo) $ echo "1111" > 1.txt
    [dimsenv] dittrich@ren:/tmp/testing (foo*) $ git commit -am "Second edit"
    [foo 0b0e0a9] Second edit
     1 file changed, 1 insertion(+), 1 deletion(-)

..

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (foo) $ git rm 3.txt
    rm '3.txt'
    [dimsenv] dittrich@ren:/tmp/testing (foo*) $ git commit -m "Removed file"
    [foo 0a522af] Removed file
     1 file changed, 1 deletion(-)
     delete mode 100644 3.txt

..

If we now look at the commit history, we see the initial commit
where we branched off, and the three change commits.

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (foo) $ git gr
    * commit 0a522af0d8c09d206f37b647014628a89070fe94 (HEAD -> foo)
    | Author: Dave Dittrich <dittrich@u.washington.edu>
    | Date:   Mon Sep 5 17:59:36 2016 -0700
    |
    |     Removed file
    |
    * commit 0b0e0a986e228b177e8775900198c99af80ef5f2
    | Author: Dave Dittrich <dittrich@u.washington.edu>
    | Date:   Mon Sep 5 17:58:34 2016 -0700
    |
    |     Second edit
    |
    * commit 71738c7b1d2f504110190eaca3c71461e7090cc6
    | Author: Dave Dittrich <dittrich@u.washington.edu>
    | Date:   Mon Sep 5 17:58:19 2016 -0700
    |
    |     First edit
    |
    * commit 3ee79c4d4455a5517a93ce7e02db88d3db7934f4 (master)
      Author: Dave Dittrich <dittrich@u.washington.edu>
      Date:   Mon Sep 5 17:57:51 2016 -0700

          Initial commit

..

We now start an interactive rebase, referencing the hash of the
initial commit (the one right before all of the changes on the
branch).

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (foo) $ git rebase -i 3ee79c4

    pick   71738c7 First edit
    squash 0b0e0a9 Second edit
    squash 0a522af Removed file

    # Rebase 3ee79c4..0a522af onto 3ee79c4 (       3 TODO item(s))
    #
    # Commands:
    # p, pick = use commit
    # r, reword = use commit, but edit the commit message
    # e, edit = use commit, but stop for amending
    # s, squash = use commit, but meld into previous commit
    # f, fixup = like "squash", but discard this commit's log message
    # x, exec = run command (the rest of the line) using shell
    #
    # These lines can be re-ordered; they are executed from top to bottom.
    #
    # If you remove a line here THAT COMMIT WILL BE LOST.
    #
    # However, if you remove everything, the rebase will be aborted.
    #
    # Note that empty commits are commented out

..

When you save the file out of the editor, Git will perform the rebase
operation:

.. code-block:: none

    [detached HEAD 5f90a71] First edit
     Date: Mon Sep 5 17:58:19 2016 -0700
     3 files changed, 2 insertions(+), 3 deletions(-)
     delete mode 100644 3.txt
    Successfully rebased and updated refs/heads/foo.

..

You can now see the commit history has been changed to reflect only
one commit (with a combined comment indicating all of the actions
from each commit, since we didn't alter any of the comments while
we did the squashing).

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (foo) $ git gr
    * commit 5f90a717f96501ba6526a83c107302e0fbc30f10 (HEAD -> foo)
    | Author: Dave Dittrich <dittrich@u.washington.edu>
    | Date:   Mon Sep 5 17:58:19 2016 -0700
    |
    |     First edit
    |
    |     Second edit
    |
    |     Removed file
    |
    * commit 3ee79c4d4455a5517a93ce7e02db88d3db7934f4 (master)
      Author: Dave Dittrich <dittrich@u.washington.edu>
      Date:   Mon Sep 5 17:57:51 2016 -0700

          Initial commit

..

We can now merge the single resulting commit back into the ``master``
branch.

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (foo) $ git checkout master
    Switched to branch 'master'
    [dimsenv] dittrich@ren:/tmp/testing (master) $ git merge foo
    Updating 3ee79c4..5f90a71
    Fast-forward
     1.txt | 2 +-
     2.txt | 2 +-
     3.txt | 1 -
     3 files changed, 2 insertions(+), 3 deletions(-)
     delete mode 100644 3.txt

..

Our changes have taken effect:

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (master) $ ls
    1.txt   2.txt
    [dimsenv] dittrich@ren:/tmp/testing (master) $ cat *
    1111
    22222

..

The ``HEAD`` has now moved and ``master`` and ``foo`` are
at the same point. (We can now delete the ``foo`` branch.)

.. code-block:: none

    [dimsenv] dittrich@ren:/tmp/testing (master) $ git gr
    * commit 5f90a717f96501ba6526a83c107302e0fbc30f10 (HEAD -> master, foo)
    | Author: Dave Dittrich <dittrich@u.washington.edu>
    | Date:   Mon Sep 5 17:58:19 2016 -0700
    |
    |     First edit
    |
    |     Second edit
    |
    |     Removed file
    |
    * commit 3ee79c4d4455a5517a93ce7e02db88d3db7934f4
      Author: Dave Dittrich <dittrich@u.washington.edu>
      Date:   Mon Sep 5 17:57:51 2016 -0700

          Initial commit

..

.. _keepingBranchesUpToDate:

Merging changes from ``develop`` into feature branches to keep up to date
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When branches live for a long time, and changes occur to what should be
the stable ``develop`` branch, those branches start to drift and can
become "broken" because things like hotfixes and new features are not
present on the feature branch. To avoid this, first try to *not have
feature branches live for a long time*, and second, merge the develop
branch into the feature branches when needed.

Start by reading these pages:

* `Commit Often, Perfect Later, Publish Once\: Git Best Practices`_
* `Git Tips`_

.. _Commit Often, Perfect Later, Publish Once\: Git Best Practices: http://sethrobertson.github.io/GitBestPractices/
.. _Git Tips: http://mislav.uniqpath.com/2010/07/git-tips/

.. code-block:: none

    $ git checkout pyes
    Switched to branch 'pyes'
    Your branch is up-to-date with 'origin/pyes'.
    [dittrich@localhost ansible-playbooks (pyes)]$ git rebase dev
    First, rewinding head to replay your work on top of it...
    Applying: Add pyes pip install to ELK role
    Applying: Add marker to prevent re-loading of existing data and don't add Team Cymru data
    Applying: Fix bug in demo.logstash.deleteESDB script
    Applying: Add feedback message if data already loaded
    Applying: Remove debug flag from data pre-load play
    Applying: Add demo.firefox.setup changes
    Applying: Make default for Kibana be the UFW dataset
    Applying: Add curl to base role
    Using index info to reconstruct a base tree...
    M	roles/base-os/tasks/main.yml
    Falling back to patching base and 3-way merge...
    Auto-merging roles/base-os/tasks/main.yml
    Applying: Add elasticutils Python library to ELK role
    Applying: Add play to pin Gnome Terminal to panel
    Applying: Fix Ansible syntax error re: Gnome Terminal fix
    Applying: Fix typo in Gnome Terminal script setup
    Applying: Add dconf-tools package for dconf program
    Applying: Run Gnome Terminal pin script with sudo
    Applying: Fix for dbus-launch failure
    Applying: Fix bug in pin-gnome-terminal script re: dconf write operation
    Applying: Fix bug in pin-gnome-terminal script
    Applying: Fix bug in pin-gnome-terminal re: empty 'value' variable</verbatim>

..

This is what the ``pyes`` branch looked like before:

.. _pyesBeforeRebase:

.. figure:: images/pyes-before-rebase.png
   :alt: Branch ``pyes`` before rebase
   :width: 80%
   :align: center

   Branch ``pyes`` before rebase

..

This is what the ``pyes`` branch looked like after the rebase:

.. _pyesAfterRebase:

.. figure:: images/pyes-after-rebase.png
   :alt: Branch ``pyes`` after rebase
   :width: 80%
   :align: center

   Branch ``pyes`` after rebase

..

Notice the numbers ``+23-19`` in the after image? We just rebased commits from
the local repo branch ``develop`` onto the local repo branch ``pyes``. We
haven't done anything yet with the remote repo. The numbers mean that after
rebasing to get the missing commits, there are commits that exist on the local
repo that do not exist on the remote repo, and vice-versa.

If we now try to push the ``pyes`` branch, Git complains that it can't because
there are remote changes that are not in the local repo that need to be merged
and checked for possible conflict before the push can proceed.


.. code-block:: none

    $ git push
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
     ! [rejected]        pyes -> pyes (non-fast-forward)
    error: failed to push some refs to 'git@git.devops.develop:/opt/git/ansible-playbooks.git'
    hint: Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. Integrate the remote changes (e.g.
    hint: 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.</verbatim>

..

Doing a ``git pull`` first, then a ``git push`` results in a clean rebase of the remote
commits with the local commits (which are now up to date on the ``pyes`` feature
branch in relation to the ``develop`` branch.)

.. code-block:: none

    $ git pull
    First, rewinding head to replay your work on top of it...
    Applying: Added schema.psl to populate dims database from ops-trust
    Applying: added postgres-dims role and files
    Using index info to reconstruct a base tree...
    M	dims-global-server.yml
    <stdin>:94: trailing whitespace.
    -- Name: plpgsql; Type: EXTENSION; Schema: -; Owner:
    <stdin>:101: trailing whitespace.
    -- Name: EXTENSION plpgsql; Type: COMMENT; Schema: -; Owner:
    <stdin>:210: trailing whitespace.
    -- Name: attestations; Type: TABLE; Schema: public; Owner: postgres; Tablespace:
    <stdin>:224: trailing whitespace.
    -- Name: audit_history; Type: TABLE; Schema: public; Owner: postgres; Tablespace:
    <stdin>:237: trailing whitespace.
    -- Name: language_skill; Type: TABLE; Schema: public; Owner: postgres; Tablespace:
    warning: squelched 50 whitespace errors
    warning: 55 lines add whitespace errors.
    Falling back to patching base and 3-way merge...
    Auto-merging dims-global-server.yml
    Applying: Add curl to all hosts in base-os role
    Using index info to reconstruct a base tree...
    M	roles/base-os/tasks/main.yml
    Falling back to patching base and 3-way merge...
    Auto-merging roles/base-os/tasks/main.yml
    Applying: Add curl to base role
    Using index info to reconstruct a base tree...
    M	roles/base-os/tasks/main.yml
    Falling back to patching base and 3-way merge...
    No changes -- Patch already applied.
    [dittrich@localhost ansible-playbooks (pyes)]$ git push
    Counting objects: 15, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (14/14), done.
    Writing objects: 100% (15/15), 392.72 KiB | 0 bytes/s, done.
    Total 15 (delta 9), reused 3 (delta 0)
    remote: Running post-receive hook: Tue Nov  4 18:12:01 PST 2014
    To git@git.devops.develop:/opt/git/ansible-playbooks.git
       9b23575..2166e16  pyes -> pyes</verbatim>

..


.. _permanentremoval:

Permanently Removing Files from a Git Repo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Before publishing once private repositories on an internal Git repo server to
a public server like GitHub requires making sure that **all** sensitive
data is premanantly removed from the local repository's history **before**
first pushing it to GitHub. Just going a ``git rm file`` on a file will remove
it from the working directory, but not from Git's history. It still exists
in a previous commit in the repo.

A common reason for purging files from the Git history is when someone
commits many large binary archive files (e.g., some source packages,
operating system installation ISOs, etc). Those files exist in their
original distribution servers and mirrors, so there isn't a need to put
them under revision control. They just make the Git repo larger for
no good reason.

.. danger::

   Realize that if you are trying to permanently remove secrets, such
   as passwords or encryption private keys, even doing these steps is
   not enough.  Read what GitHub has to say in the Danger block at the top of their
   `Remove sensitive data`_ page. In short, any time extremely sensitive
   data (like a password or private key) is published to GitHub, it
   should **be considered compromised**, reported to the project lead,
   and changed as soon as possible.

..

   + `How to delete files permanently from your local and remote git repositories`_, by Anoopjohn, February 20, 2014
   + GitHub `aaronzirbes/shrink-git-repo.sh`_ ("This script will help you remove large files from your git repo history and shrink the size of your repository.")
   + `How to Shrink a Git Repository`_, by Steve Lorek, May 11, 2012

The page `How to Shrink a Git Repository`_ was used successfully to perform
cleanup of a large number of archives that were committed to the
``ansible-playbooks`` repo. The string ``filename`` needed to be substituted
with the paths of the files to delete, which were identified by the script
``git-find-largest`` and edited with ``vi`` and ``awk`` to strip out just the
paths. The following command was then used on the list:

.. code-block:: bash

    for f in $(cat largest.txt); do \
      git filter-branch --tag-name-filter cat \
        --index-filter "git rm -r --cached --ignore-unmatch $f" \
        --prune-empty -f -- --all; \
    done

..

After that, the steps to clear the cache, do garbage collection and pruning, etc. were followed.

See also GitHub's `Remove sensitive data`_ page to use
either ``git filter-branch`` or the `BFG Repo-Cleaner`_ to remove
files from a clone of the repo and then push the clean version
to GitHub.

.. code-block:: none

    bfg 1.12.15
    Usage: bfg [options] [<repo>]

      -b, --strip-blobs-bigger-than <size>
                               strip blobs bigger than X (eg '128K', '1M', etc)
      -B, --strip-biggest-blobs NUM
                               strip the top NUM biggest blobs
      -bi, --strip-blobs-with-ids <blob-ids-file>
                               strip blobs with the specified Git object ids
      -D, --delete-files <glob>
                               delete files with the specified names (eg '*.class', '*.{txt,log}' - matches on file name, not path within repo)
      --delete-folders <glob>  delete folders with the specified names (eg '.svn', '*-tmp' - matches on folder name, not path within repo)
      --convert-to-git-lfs <value>
                               extract files with the specified names (eg '*.zip' or '*.mp4') into Git LFS
      -rt, --replace-text <expressions-file>
                               filter content of files, replacing matched text. Match expressions should be listed in the file, one expression per line - by default, each expression is treated as a literal, but 'regex:' & 'glob:' prefixes are supported, with '==>' to specify a replacement string other than the default of '***REMOVED***'.
      -fi, --filter-content-including <glob>
                               do file-content filtering on files that match the specified expression (eg '*.{txt,properties}')
      -fe, --filter-content-excluding <glob>
                               don't do file-content filtering on files that match the specified expression (eg '*.{xml,pdf}')
      -fs, --filter-content-size-threshold <size>
                               only do file-content filtering on files smaller than <size> (default is 1048576 bytes)
      -p, --protect-blobs-from <refs>
                               protect blobs that appear in the most recent versions of the specified refs (default is 'HEAD')
      --no-blob-protection     allow the BFG to modify even your *latest* commit. Not recommended: you should have already ensured your latest commit is clean.
      --private                treat this repo-rewrite as removing private data (for example: omit old commit ids from commit messages)
      --massive-non-file-objects-sized-up-to <size>
                               increase memory usage to handle over-size Commits, Tags, and Trees that are up to X in size (eg '10M')
      <repo>                   file path for Git repository to clean

..

.. _How to delete files permanently from your local and remote git repositories: http://www.zyxware.com/articles/4027/how-to-delete-files-permanently-from-your-local-and-remote-git-repositories
.. _aaronzirbes/shrink-git-repo.sh: https://gist.github.com/aaronzirbes/4570924
.. _How to Shrink a Git Repository: http://stevelorek.com/how-to-shrink-a-git-repository.html
.. _Remove sensitive data: https://help.github.com/articles/remove-sensitive-data
.. _BFG Repo-Cleaner: http://rtyley.github.io/bfg-repo-cleaner/


.. todo::

   STOPPED HERE (see ``not-added-yet.txt`` for more text to be added.)

..

.. _gitandsecrets:

Git and Secrets
---------------

There are a plethora of ways to deal with secrets in relation to source
code in public Git repos.

Some groups chose to separate out the secrets into repos that are not made
public (i.e., one public repo without secrets, and one private repo with only
the secrets). This adds some complexity and requires multiple Git repository
locations that clearly separate the private and public repos.

Other groups may prefer to keep their repositories small in number and simple,
using a single directory tree with the secrets being encrypted within that
tree.  At one extreme of the "secrets in the repo" mechanism require
encrypting the entire repo, while at the other end only a limited number of
specific secrets are encrypted, leaving the majority of the repository in
clear text form.

The DIMS project started out with lots of Git repos that were narrowly focused
on specific system components to try to modularize in a way that facilitated
integrating open source tools written by other groups. The primary repository
that needed secrets was the Ansible playbooks repository.

.. attention::

    Regardless of which mechanism for managing secrets you chose, *everyone*
    with Git commit rights *must* have discipline when it comes to handling
    secrets.  It only takes a few mistakes to cause a lot of cleanup headaches,
    or for an accidental commit followed by a missed review and unintended push
    to result in a password or key exposure crisis.

    The DIMS project ran into this problem many times, with accidental commits
    including private keys, passwords, and unredacted sample data from
    production systems. It wasn't until the repos were going to be made
    public that reviews identified several of these mistakes, causing long
    delays while cleanup activities were added to code completion tasks.

    There is a cost/benefit tradeoff that must be made between using more than
    just one shared "development" repository location (to more closely vet and
    control commits to the "official" repository location) vs. the time and
    effort required to sanitize accidentally committed secrets and
    simultaneously delete all clones at the same time to prevent the secrets
    being accidentally pushed back into the repo.

..

The two mechanisms first tested by the DIMS project were:

    * `Ansible Vault`_
    * `git-crypt`_


Ansible Vault
~~~~~~~~~~~~~

Ansible Vault is a command-line tool provided by Ansible.  It allows for
application of encryption at a very granular level.

Vault is a password-based encryption system. This password can be stored in
a file, but it must be shared to every user who is allowed access to the
secret data files that are encrypted. This means there is still one step of
having to figure out how to share the vault password.

Once the password is known by all parties, the process is pretty simple. To
create a file you want to be encrypted,

.. code-block:: none

    $ ansible-vault create --vault-password-file=$PASS_FILE vaultfile.yml

..

To view the file, without being able to edit it,

.. code-block:: none

    $ ansible-vault view --vault-password-file=$PASS_FILE vaultfile.yml

..

To edit the file,

.. code-block:: none

    $ ansible-vault edit --vault-password-file=$PASS_FILE vaultfile.yml

..

To encrypt a file,

.. code-block:: none

    $ ansible-vault encrypt --vault-password-file=$PASS_FILE newvaultfile.yml

..

To decrypt a file,

.. code-block:: none

    $ ansible-vault decrypt --vault-password-file=$PASS_FILE newvaultfile.yml

..

When you commit a vault-protected file, it will be the encrypted file that is
committed to Git. Thus, if you ``decrypt`` a file to view it, you'll have to
``encrypt`` it again, and the file will change, so you'll have to commit it
again.

If you need to rekey a file,

.. code-block:: none

    $ ansible-vault rekey --new-vault-password-file=$NEW_PASS_FILE rekeyvaultfile.yml

..

To use Ansible to share secret information, one way is by copying an entire
file of secrets. First, you must load a file with the secret information.
This is generally done by creating a dictionary of information, including the
destination of the secret file, the owner and group and mode of the file, as
well as the actual contents of the file. You then run a task to load the secret
file, which is decrypted with the Vault password and read by Ansible, then the
information from that file is used to create the new file on the target machine.


.. warning::

    One important thing to note is that you must use the ``no_log`` module in these
    types of tasks to keep Ansible from printing the secret information in the
    output of running plays.

..

.. code-block:: none

    - name: Load secret password file
      include_vars: "vault.yml"
      no_log: true
      when: ansible_os_family == "Debian"
      tags: [ ansible-server ]

    - name: Copy secret password file
      copy:
        dest: "{{ item.key }}"
        content: "{{ item.value.content }}"
        owner: "{{ item.value.owner }}"
        group: "{{ item.value.group }}"
        mode: "{{ item.value.mode }}"
      with_dict: "{{ vault_password }}"
      when: ansible_os_family == "Debian"
      no_log: true
      tags: [ ansible-server ]

..

The following is an example of the ``vault.yml`` file:

.. code-block:: none

    ---

    # File: unencrypted version of vault.yml

    password:
      /home/ansible/pass.txt
        owner: "ansible"
        group: "ansible"
        mode: "u=r,go="
        content: |
          Secretsecretsecret

    # eof

..

Additionally, you can use vault to keep variables secret that may not be
used to create a whole file, like an SSH key. For example, a username and
password might be needed in a service's configuration file. All you need
to do is create a Vault-encrypted file with those secrets, include the
secret vars file in a task before needing to use those secret variables
(say, in a template), and then the secret will be populated on the target
machine.

However, unless using the ``ansible-vault view`` command, the secret
variables file is encrypted, so it isn't searchable. A solution to this
problem is to include a secret variable in a "normal" vars file, but
don't include the actual secret there--set that variable to another
variable.

Let's say we need a username and password for Service X that is going to
run on several machines in deployment ``local``. In our group_vars file
for this deployment, ``deployment-local.yml``, we'd define the
username and password variables for Service X as follows:

.. code-block:: none

    ...

    serviceXUsername: "{{ vault_serviceXUsername }}"
    serviceXPassword: "{{ vault_serviceXPassword }}"

    ...

..

We would use the ``ansible-vault create`` command to then define the
``vault_*`` variables with their actual secret data, as follows:

.. code-block:: none

    ---

    # File: unencrypted version of deployment-local-vault.yml

    vault_serviceXUsername: "secretUsername"
    vault_serviceXPassword: "secretPassword"

    # eof
..

Now, the secret variables file has to be included before any variable
defined within it is used:

.. code-block:: none

    - name: Load deployment secret variables file
      include_vars: "../../../inventory/group_vars/deployment-local-vault.yml"
      no_log: true
      when: ansible_os_family == "Debian"
      tags: [ ansible-server ]

    - name: Use secret variables
      template: "src=test-secret.yml dest=/etc/test-secret.yml owner=root group=root mode={{ mode_0644 }}"
      no_log: true
      sudo: yes
      when: ansible_os_family == "Debian"
      tags: [ ansible-server ]

..

Helpful Links:

    * http://docs.ansible.com/ansible/playbooks_best_practices.html#variables-and-vaults

    * https://www.onwebsecurity.com/devops/safely-storing-ansible-playbook-secrets

    * https://dantehranian.wordpress.com/2015/07/24/managing-secrets-with-ansible-vault-the-missing-guide-part-1-of-2/

    * https://dantehranian.wordpress.com/2015/07/24/managing-secrets-with-ansible-vault-the-missing-guide-part-2-of-2/

    * https://serversforhackers.com/video/ansible-using-vault

git-crypt
~~~~~~~~~

Another granular approach we looked at was ``git-crypt``. This integrates
encryption using GPG keys within the ``git`` ecosystem. It allows
for encryption by file, rather than repo. In addition, since the encryption
integrated in the ``git`` ecosystem, you don't have to use special
commands to view or edit encrypted files; encrypted files are decrypted
when a repo is checked out and encrypted when committed.

The trouble we have found is a problem many projects will have, especially
open source ones. The files are encrypted with the GPG keys available at
the time of a given file's encryption. This means that if other project
members join after a file is encrypted, that file will have to be re-encrypted
once the new member's GPG key has been integrated. All encrypted files
would ahve to be ID'd and then re-encrypted. Additionally, this also
compounds the key revocation problem as whoever has a private key to
decrypt the files will still be able to see the old commits. The files
can't just be re-encrypted, all the secrets they contain must be changed.

At this time, we have chosen to stick with ``Vault`` over ``git-crypt``
for the reasons listed above.

.. _git-crypt: https://github.com/AGWA/git-crypt
.. _Ansible Vault: http://docs.ansible.com/ansible/playbooks_vault.html
.. _github.com/uw-dims: https://github.com/uw-dims
.. _ReadTheDocs: https://readthedocs.org/

Git and Unix permissions
~~~~~~~~~~~~~~~~~~~~~~~~

A final issue that must be known and kept in mind is the limitation in
Git's ability to properly handle Unix permission masks. Unix permission
masks (or ``umask``) are bit masks that handle multiple permissions
(i.e., ``read``, ``write``, and ``execute``) for multiple groups
(i.e., ``owner``, ``group``, and ``other``), along with some other
permission bits (``setgid``, ``setuid``, and ``sticky`` bits).

Git only pays attention to the ``execute`` permissions, and does so
in a limited way. That means a file may have a mode ``0755`` (write
permission only for user, but universal read and execute permission),
or it may have a mode ``0644`` (same as above, but no execute permission
for anyone).

To see this problem in action, we will use the ``keys.host.create`` script
to generate some SSH key pairs. SSH only allows keys to be used if
they have *no* read permission for ``other``, so it generates keys
with permission mask ``0600`` (see highlighted lines):

.. code-block:: none
   :emphasize-lines: 6,8,10,12

    $ keys.host.create -d . -p EXAMPLE
    $ ls -l
    total 40
    -rw-rw-r-- 1 dittrich dittrich  358 Mar 13 12:17 key_fingerprints.txt
    -rw-rw-r-- 1 dittrich dittrich 1304 Mar 13 12:17 known_hosts.add
    -rw------- 1 dittrich dittrich  668 Mar 13 12:17 ssh_host_dsa_key
    -rw-r--r-- 1 dittrich dittrich  616 Mar 13 12:17 ssh_host_dsa_key.pub
    -rw------- 1 dittrich dittrich  365 Mar 13 12:17 ssh_host_ecdsa_key
    -rw-r--r-- 1 dittrich dittrich  282 Mar 13 12:17 ssh_host_ecdsa_key.pub
    -rw------- 1 dittrich dittrich  432 Mar 13 12:17 ssh_host_ed25519_key
    -rw-r--r-- 1 dittrich dittrich  112 Mar 13 12:17 ssh_host_ed25519_key.pub
    -rw------- 1 dittrich dittrich 1675 Mar 13 12:17 ssh_host_rsa_key
    -rw-r--r-- 1 dittrich dittrich  408 Mar 13 12:17 ssh_host_rsa_key.pub

..

Now, add the keys to Git and watch the permissions that Git gives
to those files:

.. code-block:: none
   :emphasize-lines: 7,9,11,13

    $ git add .
    $ git commit -m 'Add keys'
    [master (root-commit) 47f872b] Add keys
     10 files changed, 66 insertions(+)
     create mode 100644 keys/key_fingerprints.txt
     create mode 100644 keys/known_hosts.add
     create mode 100644 keys/ssh_host_dsa_key
     create mode 100644 keys/ssh_host_dsa_key.pub
     create mode 100644 keys/ssh_host_ecdsa_key
     create mode 100644 keys/ssh_host_ecdsa_key.pub
     create mode 100644 keys/ssh_host_ed25519_key
     create mode 100644 keys/ssh_host_ed25519_key.pub
     create mode 100644 keys/ssh_host_rsa_key
     create mode 100644 keys/ssh_host_rsa_key.pub

..

As you can see, all files that did not have the ``execute`` bit got a
permissions mask of ``100644``, so all files that were ``0600`` will end up
being pulled with a permissions mask of ``0644``.  SSH will not allow a
permission mask of ``0644`` on private keys, so if these were user keys SSH
would not allow them to be used.  To ensure that checking out or pulling these
files will still work requires an extra step to fix these permissions, which is
a little complicated and involves the use of Git hooks.

The simplest method is to use the public/private pairing to identify all files
that end in ``.pub`` and change the mode to ``0600`` on the file with the
``.pub`` extension stripped (i.e., the associated private key). A script to do
this may look something like this:

.. code-block:: none

    #!/bin/bash
    echo "[+] Verifying private key permissions and correcting if necessary"
    find * -type f -name '*.pub' |
      while read pubkey; do
        privkey=$(dirname $pubkey)/$(basename $pubkey .pub)
        if [[ -f $privkey ]]; then
          mode=$(stat -c %a $privkey)
          if [[ $? -ne 0 ]]; then
            echo "[-] Failed to get mode for $privkey"
          elif [[ "$mode" != "600" ]]; then
            echo "[+] Changing mode $mode to 600 on file $privkey"
	    chmod 600 $privkey
          fi
        fi
      done
    exit 0

..

.. attention::

    You cannot add files in the ``.git/hooks`` directory, where hooks are
    found for execution by Git, to Git tracking. The ``.git`` directory
    is _not_ part of the Git repository commit structure. You can add
    a directory named ``hooks/`` at the top level of the tracked repo
    and create links into the ``.git/hooks`` directory. This has to
    be done at least once per clone of the repo, which is up to the
    person doing the clone to perform manually (or to use wrapper
    scripts around Git that do this on the initial clone operation).

    Here is a ``Makefile`` that automates this process:

    .. code-block:: none

        .PHONY: all
        all: install-hooks

        .PHONY: install-hooks
        install-hooks:
                @(cd hooks; find * -type f -o -type l 2>/dev/null) | \
                        while read hook; do \
                                echo "[+] Installing .git/hooks/$$hook"; \
                                ln -sf ../../hooks/$$hook .git/hooks/$$hook; \
                        done

    ..

..

To preserve the actual permission masks in a general way for all files
being committed to Git is much more complicated and goes beyond the needs
of this particular issue. Examples of how to do this are found in
the following references, or search for other options.

* `githooks - Hooks used by Git`_
* `How To Use Git Hooks To Automate Development and Deployment Tasks`_
* `Retaining file permissions with Git`_

.. _githooks - Hooks used by Git: https://git-scm.com/docs/githooks
.. _How To Use Git Hooks To Automate Development and Deployment Tasks: https://www.digitalocean.com/community/tutorials/how-to-use-git-hooks-to-automate-development-and-deployment-tasks
.. _Retaining file permissions with Git: http://stackoverflow.com/questions/3207728/retaining-file-permissions-with-git

