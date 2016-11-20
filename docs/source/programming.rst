.. _programming:

Programming Conventions
=======================

This chapter includes examples of programming practices, pointers to
code that can serve as a starting point for new programs, and
other references to help DIMS developers (and their products)
be consistent, maximally functional, robust, and professional.

.. _helpermakefiles:

Use of ``Makefile`` helpers
---------------------------

The Unix `make`_ utility is used heavily in DIMS development as a means of
scripting command sequences in a way that simplifies tasks and makes them more
repeatable and consistent. Rather than producing a ``README`` file that lists
generic examples of long list of command lines that must be altered slightly
for a specific purpose (e.g., naming things uniquely), a ``Makefile`` may be
used that has a single target that identified by a meaningful word like
"target" that can be invoked with one easy to remember command:

.. _make: https://en.wikipedia.org/wiki/Make_(software)

.. code-block:: none

    $ make target

..

A DIMS ``Makefile`` helper library is available (and encouraged to be used)
to consolidate frequently needed variables, macros, etc., into a single file
that can be included at the top of every DIMS ``Makefile``. One of the things
this library includes is a mechanism to embed self-documenting text that
can be output when you type ``make help``. Here is an example from the 
``dims-ci-utils/dims/`` directory:

.. code-block:: none

    [dittrich@27b dims (develop)]$ make help
    /Users/dittrich/dims/git/dims-ci-utils/dims
    [Using Makefile.dims.global v1.6.22 rev 1.4-295-g38b22c8]
    ---------------------------------------------------------------------------
    Usage: make [something]
    
    Where "something" is one of the targets listed in the sections below.
    
     ---------------------------------------------------------------------------
                     Targets from Makefile.dims.global
    
     help - Show this help information (usually the default rule)
     dimsdefaults - show default variables included from Makefile.dims.global
     version - show the Git revision for this repo
     envcheck - perform checks of requirements for DIMS development
     ---------------------------------------------------------------------------
                       Targets from Makefile
    
     bootstrap - install foundation items (i.e., test_functions.sh,
                 test.dims-ci-utils.envcheck, and test.dims-ci-utils.installcheck)
     install - install programs/files
     manifest - create expected results (a manifest, for lack of
                a better term) for installcheck to use.
                (See also: README.installcheck)
     uninstall - remove programs/files
     installdirs - create required directories
     installcheck - test to see if everything installed properly
     clean - Remove manifest.txt file.
     test - run test.dims-ci-utils.envcheck to test test_functions.sh
    ---------------------------------------------------------------------------
    
..

The first few lines of ``$GIT/dims-ci-utils/dims/Makefile`` are shown here,
with the ``include`` statement and help text for the target ``bootstrap``:

.. code-block:: make
   :emphasize-lines: 8,9,11,12

    # Makefile for DIMS test scripts and other common
    # continuous integration aids.

    # The following include line is special for
    # this one Makefile, since it is used to manage
    # Makefile.dims.global.  Other Makefile invocations
    # should use:
    # include $(DIMS)/etc/Makefile.dims.global
    include Makefile.dims.global
    
    #HELP bootstrap - install foundation items (i.e., test_functions.sh,
    #HELP             test.dims-ci-utils.envcheck, and test.dims-ci-utils.installcheck)
    .PHONY: bootstrap
    bootstrap: installdirs \
    	$(DIMSETC)/Makefile.dims.global \
    	$(DIMSBIN)/test_functions.sh \
    	$(DIMSBIN)/test.dims-ci-utils.envcheck \
    	$(DIMSBIN)/test.dims-ci-utils.installcheck
    
    ...

..

.. _variablenaming:

Variable Naming Conventions
---------------------------

Because the DIMS project uses several programming languages, and several open source
tools that may have their own naming policies, variable naming is sometimes quiet
hard to normalize into a single form.  Programmers also came to the project with
their own preferences and past practices, combined with a desire to make their
own mark and exercise their own creativity. The result is a mixture of naming
conventions and variable name choices that are not entirely consistent.

On top of this, the goal of *no hard coded values* means that variables are often
used to create other variables in order to break down something as complicated as
a Uniform Resource Locator (URL) that has protocol, host name or address, port
number, directory path, and file name.  In order to refer to (a) the complete URL,
(b) just the file name, and (c) the specific version number, requires that
many (but not *too many*) variables be defined.

Some of these variables will also be specific to the program being used (e.g.,
"consul" for the Consul distributed key/value store program), and some are
shared with the operating system and/or hardware architecture (e.g., "ubuntu"
and "amd64").  It is desireable to not have multiple variables that hold the
same value, but sometimes necessary to have two variables that have slightly
different (but related) values (e.g., ``ansible_architecture`` may hold
``x86_64``, but a program like Consul may use ``amd64`` as the architecture
identifier. The following is the full target URL for the Consul binary:

.. code-block:: none

     https://releases.hashicorp.com/consul/0.6.4/consul_0.6.4_linux_amd64.zip

..


This can be broken down into the following variables:

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Variable
     - Example Value(s)

   * - ``consul_version``
     - ``0.6.4``

   * - ``consul_os``
     - ``linux`` (for Ubuntu, Debian), ``darwin`` (for Mac OS X)

   * - ``consul_arch``
     - ``amd64`` (equivalent to ``ansible_architecture`` value ``x86_64``)

   * - ``consul_artifact``
     - ``consul_{{ consul_version }}_{{ consul_os }}_{{ consul_arch }}.zip``

   * - ``consul_dist_url``
     - ``https://releases.hashicorp.com/consul/{{ consul_version }}/``

..

The templated version of an Ansible play that downloads the distribution
artifact for Consul would thus look like:

.. code-block:: jinja

    - name: Make Consul distribution artifact present
      get_url:
        url={{ consul_dist_url }}/{{ consul_artifact }}
        dest={{ dims_deploy }}/{{ role_name }}
        sha256sum={{ consul_sha256sum }}
      sudo: yes
      tags: [ consul ]

..

Using variable names that begin with a consistent string identifying the role or
program that they correspond with also helps in two ways.

First, it groups the names (when sorted) and differentiates variables with the
same function (e.g., identifying a version number) between Ansible roles.
Since Ansible lumps all variables into the same name space, and may process
roles in unpredicatable order, there is a chance that a variable with the same
exact name in two or more roles will *not* have the value that you think it
will have when the play is actually performed.  Names must be either prefixed
with a string that differentiates them (e.g., ``consul_version`` vs.
``docker_version``), or a more complex nested data structure must be used.
Since the latter is more complex, harder to read and harder for those who are
new to Ansible to remember how to use, prefixing and separation by way of
underscore characters is the preferable naming scheme.

Secondly, this naming style matches that already used by other Ansible
facts, which makes the Ansible debugging output a little more consistent
and easier to read, again primarily because of the lexical grouping
that results.

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~/dims/git/dims-devguide/docs (develop*) $ ls /bin
    bash                  dmesg          loginctl          ntfsls                   stty
    bunzip2               dnsdomainname  lowntfs-3g        ntfsmftalloc             su
    busybox               domainname     ls                ntfsmove                 sync
    bzcat                 dumpkeys       lsblk             ntfstruncate             tailf
    bzcmp                 echo           lsmod             ntfswipe                 tar
    bzdiff                ed             mkdir             open                     tempfile
    bzegrep               egrep          mknod             openvt                   touch
    bzexe                 false          mktemp            pidof                    true
    bzfgrep               fgconsole      more              ping                     udevadm
    bzgrep                fgrep          mount             ping6                    ulockmgr_server
    bzip2                 findmnt        mountpoint        plymouth                 umount
    bzip2recover          fuser          mt                plymouth-upstart-bridge  uname
    bzless                fusermount     mt-gnu            ps                       uncompress
    bzmore                getfacl        mv                pwd                      unicode_start
    cat                   grep           nano              rbash                    vdir
    cgroups-mount         gunzip         nc                readlink                 vmmouse_detect
    cgroups-umount        gzexe          nc.openbsd        red                      which
    chacl                 gzip           nc.traditional    rm                       whiptail
    chgrp                 hostname       netcat            rmdir                    ypdomainname
    chmod                 ip             netstat           rnano                    zcat
    chown                 kbd_mode       nisdomainname     running-in-container     zcmp
    chvt                  keyctl         ntfs-3g           run-parts                zdiff
    cp                    kill           ntfs-3g.probe     rzsh                     zegrep
    cpio                  kmod           ntfs-3g.secaudit  sed                      zfgrep
    dash                  less           ntfs-3g.usermap   setfacl                  zforce
    date                  lessecho       ntfscat           setfont                  zgrep
    dbus-cleanup-sockets  lessfile       ntfsck            setupcon                 zless
    dbus-daemon           lesskey        ntfscluster       sh                       zmore
    dbus-uuidgen          lesspipe       ntfscmp           sh.distrib               znew
    dd                    ln             ntfsdump_logfile  sleep                    zsh
    df                    loadkeys       ntfsfix           ss                       zsh5
    dir                   login          ntfsinfo          static-sh

..

You can see that the Zip and Bzip2 related programs all start with ``z`` and ``bz``
respectively. But just because a programs starts with "z" does **not** mean that
it is a Zip related program (e.g., ``zsh`` is a shell, completely unrelated to Zip.)
You can use tab completion in Bash or other shells to find all commands that
start with "z" and guess which ones are part of the Zip program set, but you
can't use a ``help`` sub-command to get the actual list. (See ``man zip``
to see how the documentation lists other options, though not exactly all of
them in the main ``zip`` man page.)

Another way of organizing programs (or more precisely, sub-programs) is to use a
plugin-style mechanism for organizination. Git is an example of a multi-component
program suite that does this.  It has many sub-programs, each starting with
``git-`` in the name. The Git Hubflow tools integrate themselves into Git
because their names start with ``git-``:

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~ () $ (cd $(dirname $(which git)); ls git*)
    git              git-hf          git-hf-pull     git-hf-update     git-shell
    git-big-picture  git-hf-feature  git-hf-push     git-hf-upgrade    git-upload-archive
    git-crypt        git-hf-hotfix   git-hf-release  git-hf-version    git-upload-pack
    git-cvsserver    git-hf-init     git-hf-support  git-receive-pack

..

You can use the tab command completion functionality of Bash to help show you
the grouped commands, or use the ``git help`` sub-command, which not only lists
them, but also provides even more hints on how to get help and usage
information. Here is the list of first-level sub-commands related to Git
Hubflow:

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~ () $ git hf help
    usage: git hf <subcommand>

    Available subcommands are:
       init      Initialize a new git repo with support for the branching model.
       feature   Manage your feature branches.
       release   Manage your release branches.
       hotfix    Manage your hotfix branches.
       push      Push the changes from your current branch (plus any new tags) back upstream.
       pull      Pull upstream changes down into your master, develop, and current branches.
       update    Pull upstream changes down into your master and develop branches.
       version   Shows version information.

    Try 'git hf <subcommand> help' for details.

..

This downside to this mechanism is that it requires that the top- or mid-level
command (in this case, ``git``) have complex logic and funcationality to
support this plugin model.

An easier way to acheive the same effect of allowing tab command completion
to group commands. The DIMS project uses a convention of multi-component names
separated by periods, kind of like DNS Domain names are constructed. The
first component is supposed to be the high-level grouping, followed by
sub-groups, and lastly sub-functions.

.. note::

    These scripts are installed into either ``/opt/dims/bin``, or Python
    virtual environments, as necessary to support development and testing
    without breaking the basic system functionality by accidentally installing
    a script with a syntax error that fails out when called from another system
    script. The tab command completion feature of Bash will find them, regardless
    of which directory from the ``$PATH`` they are installed in.

..

The main grouping used within DIMS is ``dims``, and another related
to Packer/Vagrant virtual machines and test/validation functionality
is ``test``.  Here is what you get when you use tab command completion
with these two prefixes:

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~ () $ test.<TAB><TAB>
    test.ansible.yaml                test.runner.orig
    test.dims-ci-utils.envcheck      test.supervisor
    test.dims-ci-utils.installcheck  test.vagrant.ansible-current
    test.md5.output                  test.vagrant.factory
    test.packer.factory              test.vagrant.list
    test.packer.list                 test.vagrant.listvms
    test.runner                      test.yaml.validate
    [dimsenv] dittrich@dimsdemo1:~ () $ dims.<TAB><TAB>
    dims.ansible-playbook         dims.elasticsearch.service    dims.localcluster.start
    dims.ansible-playbook.orig    dims.git.repoversion          dims.localcluster.status
    dims.boot2docker              dims.git.syncrepos            dims.localcluster.stop
    dims.buildvirtualenv          dims.help                     dims.makedocset
    dims.bumpversion              dims.install.createusb        dims.nas.mount
    dims.cleanup                  dims.install.dimscommands     dims.nas.umount
    dims.clusterconfig.list       dims.jj2                      dims.remote.setupworkstation
    dims.clusterconfig.local      dims.localcluster.create      dims.shutdown
    dims.clusterconfig.nas        dims.localcluster.destroy     dims.sphinx-autobuild
    dims.cluster.runscript        dims.localcluster.runscript   dims.swapcapslockctrl

..

This helps show or remind you what scripts are available and how they may be
related to each other.

.. note::

   While this naming scheme is the general policy, it is not universally
   true for all DIMS programs. Some legacy programs integrated into DIMS,
   scripts that were written for specific applications (like ``list``
   as one of the Git-shell commands), or programs written by DIMS team
   members as prototypes that were not categorized at the time they
   were written, have non-standard names.

   Also, the organizational taxonomy for naming scripts has grown organically
   over time, since this kind of organization is sometimes not readily obvious
   at the time someone needs to write a new script. Efforts to refactor these
   scripts have taken a lower priority to simply implenting those that are
   needed to complete the project.

..

An effort was begun to bring all of these scripts underneath a main CLI
the way that Git works, in order to better integrate the use of variables
that are used for differentiating between deployments. This high-level
CLI is ``dimscli`` (see :ref:`dimscli`). This is still a work-in-progress.

