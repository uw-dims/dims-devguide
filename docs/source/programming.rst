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

