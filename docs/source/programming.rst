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

