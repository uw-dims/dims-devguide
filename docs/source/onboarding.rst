.. _onboarding:

Onboarding Developers
=====================

This chapter covers the process for onboarding new developers
into the DIMS project.

.. _initialaccountsetup:

Initial Account Setup
---------------------

The first step in adding a new DIMS developer is getting them set up
with an account on our internal ``ops-trust`` portal instance.

Our FosWiki server has a page that was dedicated to the steps necessary
for `Provisioning New DIMS Users`_.

.. _Provisioning New DIMS Users: http://foswiki.prisem.washington.edu/Development/ProvisionNewUsers

.. caution::

    The FosWiki page `Provisioning New DIMS Users`_ looks like it may be out of
    date, or include steps that may not be necessary for just adding a new
    user. It has a huge number of steps that should be made more streamlined
    or added to the DIMS web app to simplify the process of adding and removing
    DIMS users in concert with the ``ops-trust`` portal at the center of DIMS.

..

Once the user has been given their password to the ``ops-trust`` portal,
they need to change their ``MemberID`` to match the account name that
should be used within DIMS. (E.g., Dave Dittrich may be given the
``MemberID`` of ``davedittrich2475`` by the portal, but the desired
account name within DIMS subsystems should be ``dittrich``.)

GPG Encryption Keys for Email, etc.
-----------------------------------

Each ``ops-trust`` portal account holder needs a GPG key to be able to
send/receive encrypted emails. In normal operation, one's ``ops-trust``
portal account is not fully enabled until the user has uploaded their
GPG key.

One of the easiest ways to process GPG-encrypted email is using `Enigmail`_
with the `The GNU Privacy Guard`_ from the `Thunderbird`_ email client. Follow
the `Enigmail Quick Start Guide`_ to install, configure, and generate a GPG key
for use with `Thunderbird`_ (which is supported on Mac, Linux, and Windows, and
is installed by default on the DIMS Ubuntu developer laptops).

After you have set up `The GNU Privacy Guard`_ and uploaded your key,
log in to the ``ops-trust`` portal and select ``PGP Keys`` from the menu
on the left of the screen to download all GPG keys for other portal
users and all email lists to which you subscribe.

.. note::

    This step will only download keys that are in the system at the time
    you press the link, which means they will get out-of-date with respect
    to new users, regenerated keys, and/or new email lists that may be
    created over time. Get in the habit of updating your GPG key ring
    regularly, or at least remember that failure to encrypt/decrypt
    and email may be due to your keyring being out of date and needing
    a refresh.

..

.. _Enigmail: https://www.enigmail.net/home/index.php
.. _The GNU Privacy Guard: https://www.gnupg.org
.. _Enigmail Quick Start Guide: https://www.enigmail.net/documentation/quickstart.php
.. _Thunderbird: https://www.mozilla.org/en-US/thunderbird/


.. _creatingaccounts:

Creating accounts
-----------------

After a new user has successfully set up their ``ops-trust`` portal
account and modified their ``MemberID`` to align with their desired DIMS
account name, they must be added to the ``dims_users`` array in
the ``$GIT/ansible-playbooks/group_vars/all`` file. Once added,
the Ansible playbook roles that generate DIMS user accounts
(e.g., ``dims-users-create``) can be played to create accounts
as needed.

.. todo::

    This step should be automated, so that adding (or removing) a user
    from the DIMS system triggers account creation (or archiving and deletion)
    automatically on DIMS-OPS systems. DIMS-DevOps systems may need to have
    this step be done manually, or triggered by adding/removing the user to/from
    one of the DevOps related trust groups.

    One method for automating this is to replace the hard-coded ``dims_users`` variable
    with a Jinja2 template, then extract the list of users from the ``ops-trust`` portal
    via an RPC call from a Python script that performs the Jinja2 template substitution
    immediately before running Ansible playbooks to create users. (A separate step
    to archive/delete terminated user accounts would be triggered by deletion
    events in the ``ops-trust`` portal, or from an administration panel accessible
    only to users in the "admin" group in the DIMS web application.)

    See :ref:`dimssr:dimssystemrequirements` sections :ref:`dimssr:accountaccesscontrols`,
    :ref:`dimssr:diutuserstory5`, and :ref:`dimssr:accountsuspension`, and
    Jira Epic `DIMS-64`_.

..

.. _DIMS-64: http://jira.prisem.washington.edu/browse/DIMS-64


.. _sshkeys:

Installing initial SSH key(s)
-----------------------------

Before someone can clone Git repositories, or use SSH to log in to DIMS
systems for interactive shell access, they must (a) have a DIMS SSH key,
and (b) have the public key and ``authorized_keys`` file(s) on target
systems set up properly.

#. Create the user's DIMS SSH key pair...

   .. todo::

      Replace this with example...

      make key
      git commit it

   ..

#. Generate accounts using Ansible playbook ($whatever), which creates
   the accounts and installs their public key.

#. Copy their key pair into the account on the system where they will be
   doing their development (i.e., a DIMS developer laptop, Vagrant virtual
   machine, or bare-metal workstation.) Also make sure their key is
   included in the ``authorized_keys`` file in the ``git`` account on
   ``git.prisem.washington.edu`` in order for them to be able to read/write
   source code using Git.

   .. todo::

      Replace this with example...

   ..

#.  Trigger a Jenkins build job for `public-keys-configure`_ to push the
    new user's key to all DIMS-DevOps and DIMS-OPS systems.


    .. todo::

        This step could be added to a script, which invokes the job using
        the REST API. That saves the manual step of clicking the
        *Build Now* button in a browser.

    ..

.. _public-keys-configure: http://jenkins.prisem.washington.edu/job/public-keys-configure/

#.  Set the password on they are supposed to use so they can log in to it,
    and/or securely transfer their public SSH key to them so they can use it
    to access the account without needing a password.

   .. todo::

      Replace this with example...

   ..

