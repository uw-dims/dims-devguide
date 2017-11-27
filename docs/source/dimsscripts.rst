.. _dimsscripts:

Developing Bash scripts
=======================

The DIMS project uses many scripts (and GNU ``make`` files) to structure,
organize, and serve as an abstraction layer wrapping other applications that
require a large number of variables or command line options to function.
Because the DIMS project attempts to support multiple independent instances of
a set of open source tools on a small-scale distributed system, the ability to
generalize operations across a set of a dozen or more servers is important.
This is not necessary to scale to one large system with hundreds or thousands
of servers, but rather to support a single small group operating multiple
replicated instances of the distributed system for *development*, *testing*,
*production for group A* and *production for group B*, etc.

Many of the basic scripts used in the DIMS project are written in Bourne Again
Shell (BASH, a.k.a. Bash and ``bash``). Bash is a relatively simple scripting
language for basic things, but quickly can get very complicated when attempting
to perform advanced tasks that are required for scalable distributed systems.

Advanced Bash programming requires understanding of some fairly low-level
Unix/Linux process and file system concepts, as well as learning how to do
run-time debugging of a scripting shell language that can be far more opaque
than languages like Python executed within Integrated Development Environments
(IDEs) and run-time debuggers.  (Bash does have a runtime debugger, but there
is a steep learning curve for those diving into advanced Bash scripting.)

.. _scriptingconventions:

DIMs programmers are advised to read, understand, and follow (to the best
of their ability) the Google `Shell Style Guide`_. Other references on
advanced Bash scripting can be found in Section :ref:`bashreferences`.

.. _Shell Style Guide: https://google.github.io/styleguide/shell.xml

.. _shflags:

Command line processing using Google's ``shFlags``
--------------------------------------------------

One of the most powerful features of Unix/Linux commands is the
ability to inheret default behaviors (e.g., through setting
environment variables that are passed from a process to its
children and children's children as scripts invoke scripts),
and to be able to alter or select options on the command line
to change the scripts' behavior.

The DIMS project uses Google's `shFlags`_ library to standardize
command line processing. Programers are required to study
the `Documentation shFlags 1.2.x.`_ page to learn how `shFlags`_
works and how to use it.

.. _shFlags: https://github.com/kward/shflags
.. _Documentation shFlags 1.2.x.: https://github.com/kward/shflags/wiki/Documentation12x

All scripts should support a common set of basic functions such
as the following:

*  ``--help`` provides help on arguments and options supported
   by the script:

   .. code-block:: none

       $ test.vagrant.list --help
       usage: test.vagrant.list [options] [ACTION] FQDN
       flags:
         -d,--[no]debug:  enable debug mode (default: false)
         -C,--category:  category identifier (default: 'devops')
         -D,--deployment:  deployment identifier (default: 'develop')
         -g,--group:  inventory group to list (default: 'vagrants')
         -r,--[no]running:  list only running vagrants (default: false)
         -s,--[no]status:  return Vagrant status and exit (default: false)
         -H,--[no]shortnames:  return short hostnames (default: false)
         -u,--[no]usage:  show usage information (default: false)
         -i,--[no]vagrants:  list vagrants from inventory (default: false)
         -m,--[no]vms:  list Virtualbox VMs (default: false)
         -v,--[no]verbose:  be verbose (default: false)
         -V,--[no]version:  print version number and exit (default: false)
         -h,--help:  show this help (default: false)

   ..

*  ``--usage`` provides more detailed information and examples of
   how to invoke the script to perform required functions:

   .. code-block:: none

       $ test.vagrant.list --usage
          usage: test.vagrant.factory [options] [ACTION] FQDN
       flags:
         -d,--[no]debug:  enable debug mode (default: false)
         -b,--[no]list-boxes:  list Vagrant boxes (default: false)
         -o,--[no]list-ovfs:  list Packer base OVF files (default: false)
         -D,--[no]list-vms:  list Virtualbox VMs (default: false)
         -s,--[no]vagrant-status:  return Vagrant status and exit (default: false)
         -P,--[no]remove-packer-box:  remove Packer box file (default: false)
         -u,--[no]usage:  show usage information (default: false)
         -v,--[no]verbose:  be verbose (default: false)
         -V,--[no]version:  print version number and exit (default: false)
         -h,--help:  show this help (default: false)

       where 'ACTION' is one of:
           build         - build a virtual machine NAME for DEPLOYMENT-CATEGORY from
                           locally checked out ansible-playbooks repo
           build-develop - build a virtual machine NAME for DEPLOYMENT-CATEGORY from
                           origin, branch dev, of ansible-playbooks (old default packer behavior)
           clean         - clean up directory for virtual machine NAME
           destroy       - destroy virtual machine NAME
           spotless      - completely remove all traces of virtual machine NAME
           get-args      - get args for test.vagrant.ansible-current
           show          - show parameters used to build virtual machine NAME
           usage         - show this help text

       ... and 'FQDN' is the fully-qualified domain name of a host defined
       in the directory /home/dittrich/dims/git/ansible-playbooks/v2/inventory/$DEPLOYMENT/host_vars/

       To get the status of a vagrant, use the --vagrant-status option:

         $ test.vagrant.factory --vagrant-status red.devops.local
         Vagrant "red" status: running


       To see status of all potential Vagrants, do:

         $ test.vagrant.factory --vagrant-status
         Vagrant "blue14" status: not created
         Vagrant "blue16" status: not created
         Vagrant "core-01" status: running
         Vagrant "core-02" status: running
         Vagrant "core-03" status: running
         Vagrant "green" status: running
         Vagrant "red" status: running
         Vagrant "yellow" status: not created

..

*  ``--version`` produces the version number, which is typically
   maintained using ``bumpversion`` (see Section :ref:`versionnumbers`
   for how ``bumpversion`` is used.)

   .. code-block:: none

       $ test.vagrant.list --version
       test.vagrant.list 1.2.28

   ..

.. _namingconventions:

Script naming conventions
-------------------------

.. todo::

    Placeholder section.

..


.. _bashreferences:

Bash programming references
---------------------------
+ `Basic grammar rules of Bash`_, BashHackersWiki
+ `Commandlinefu.com`_
    + `Find Duplicate Files (based on size first, then MD5 hash)`_
    + `Recursively remove all empty directories`_
    + GitHub `google/styleguide`_ ("Style guides for Google-originated open-source projects")
        + `Shell Style Guide`_

+ Command line option parsing
    + GitHub `kward/shflags`_ (Automatically exported from https://code.google.com/p/shflags)
    + `Easy Bash Scripting With Shflags`_, by Steve Francia, July 8, 2011
    + `Using getopts in bash shell script to get long and short command line options`_, Stackoverflow post

+ Advanced Bash scripting
    + `How "Exit Traps" Can Make Your Bash Scripts Way More Robust And Reliable`_, by Aaron Maxwell
    + `I set variables in a loop that's in a pipeline. Why do they disappear after the loop terminates? Or, why can't I pipe data to read?`_, BasFAQ/024
    + `The Ultimate Bash Array Tutorial with 15 Examples`_, by Sasikala on June 3, 2010
    + `BashGuide/Arrays`_

+ Debugging Bash scripts
    + `Debug your shell scripts with bashdb`_, by Ben Martin, November 24, 2008
    + `Debugging a script`_, Bash Hackers Wiki
    + `Why does my shell script choke on whitespace or other special characters?`_, StackExchange post by Gilles, May 24 2014



.. _Basic grammar rules of Bash: http://wiki.bash-hackers.org/syntax/basicgrammar
.. _Commandlinefu.com: http://www.commandlinefu.com/
.. _Find Duplicate Files (based on size first, then MD5 hash): http://www.commandlinefu.com/commands/view/3555/find-duplicate-files-based-on-size-first-then-md5-hash
.. _Recursively remove all empty directories: http://www.commandlinefu.com/commands/view/5131/recursively-remove-all-empty-directories
.. _google/styleguide: https://google-styleguide.googlecode.com/
.. _Shell Style Guide: https://google.github.io/styleguide/shell.xml
.. _kward/shflags: https://github.com/kward/shflags
.. _Easy Bash Scripting With Shflags: http://spf13.com/post/easy-bash-scripting-with-shflags
.. _Using getopts in bash shell script to get long and short command line options: http://stackoverflow.com/questions/402377/using-getopts-in-bash-shell-script-to-get-long-and-short-command-line-options/7680682#7680682
.. _How "Exit Traps" Can Make Your Bash Scripts Way More Robust And Reliable: http://redsymbol.net/articles/bash-exit-traps/
.. _I set variables in a loop that's in a pipeline. Why do they disappear after the loop terminates? Or, why can't I pipe data to read?: http://mywiki.wooledge.org/BashFAQ/024
.. _The Ultimate Bash Array Tutorial with 15 Examples: http://www.thegeekstuff.com/2010/06/bash-array-tutorial
.. _BashGuide/Arrays: http://mywiki.wooledge.org/BashGuide/Arrays
.. _Debug your shell scripts with bashdb: https://www.linux.com/news/debug-your-shell-scripts-bashdb
.. _Debugging a script: http://wiki.bash-hackers.org/scripting/debuggingtips
.. _Why does my shell script choke on whitespace or other special characters?: http://unix.stackexchange.com/questions/131766/why-does-my-shell-script-choke-on-whitespace-or-other-special-characters
