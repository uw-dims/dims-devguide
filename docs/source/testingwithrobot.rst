.. _testingwithrobot:

Robot Framework 
===============

This chapter briefly introduces the Robot Framework, how to
install it, how to configure it with PyCharm, and other
helpful tidbits for running Robot Framework tests.

Overview
--------

`Robot Framework`_ is an open source product developed by Google.
It is a keyword-driven framework that automates the testing
process. Robot has `builtin test`_ libraries, `external test`_ libraries,
and you can develop your own test libraries in Python or Java. 
Additionally, Robot's tests are written in natural language, and are
thus easy to develop and read.

Installation
------------

Prerequisites:

+ Python 2.7

+ python-setuptools

+ python-pip

+ python-wxgtk2.8

Then run:

.. code-block:: none

    pip install robotframework

..

Configuring with Pycharm
------------------------

For features like syntax highlighting of Robot files
and "jump to source" for Robot test library functions,
you need to install a PyCharm plugin. The one for the
Robot Framework is called `intellibot`_.

To install the intellibot plugin:

+ Go to ``Preferences > Plugins > Browse repositories``

+ Type "intellibot" in the search box

+ Click ``Download and install``

This will allow you to hover over a function defined
in some test library and click to jump to the source.
It also enables syntax highlighting of ``*.robot`` 
files.

If there are any other file types you'd like to include
in having Robot syntax highlighting, do the following:

+ Go to ``Preferences > File Types > Robot Feature Files``

+ Click the "+" in a box

+ Add ``*.txt`` or any other file extension for which you'd
  like to have Robot syntax highlighting always enabled.

There are many video tutorials for installing and configuring
Robot Framework and PyCharm, including `this one`_.


Libraries
---------

There are three main categories of test libraries: standard,
external, and yours. Standard libraries are "builtin", meaning
you don't need to "include" those files to have access to their
functions. These standard libraries are comprised of commands
that execute code to handle things like verifications,
conversions, and other multi-purpose functions.  

Most tutorials, etc. focus on testing web applications. In order
to run these tests, you need `Selenium2Library`_. This is one of
the many "external" libraries you can use. To install and use,
simply run 

.. code-block:: none

    pip install robotframework-selenium2library

..

External library files from which you'd like to access code must be 
included in the test scripts.

In addition to the standard and external libraries, you can create
your own test libraries. The Robot Framework website says "it's a
breeze". As would be expected, test library files you create and
wish to use must be included in the test scripts.

All test libraries are written in Java or Python. To learn more about
how to develop your own test libraries, check out the framework's 
`User Guide`_ section on `test library APIs`_.

Other Helpful Hints
-------------------

Examples of the following will be added in the near future.

+ Syntax

    * In keywords, use a <TAB> on lines defining commands (the list
      under the keyword heading

    * When using a variable in a keyword, put two (2) spaces between
      the keyword and the variable

+ Logic

    * It's often helpful to have a validation check at the end of a
      keyword's set of commands. For example, Once a web page is up,
      validate that a certain string you are expecting to be there
      does indeed exist.

Basic Project Structure
-----------------------

The following is the basic layout of a Robot Framework project:

.. code-block:: none

    ..
    └── project_root
        ├── run_tests.py
        ├── keywords
        │   ├── lib
        └── tests
            ├── test1.robot
            └── test2.robot

..

where ``project_root`` has the main controller for executing tasks.
This might be a Python script called "run_tests.py" as in the 
`Intro to Robot and Examples`_ tutorial does. The subfolder
``keywords`` holds keyword files of the Robot Framework commands.
The subfolder ``lib`` holds the python-based (or java-based) keyword
extensions. And the subfolder ``tests`` holds all the test scripts 
for the project.

.. TODO(mboggess)
.. todo::

    I haven't actually started a project, so I'm not sure what
    files are actually auto-populated from starting a new project.

..

Running Tests
-------------

Much, much more will be added to this section as we learn more,
but for now, here is the basic way to run a Robot test:

.. code-block:: none

    $ pybot tests/test.robot

..

This will place all the results from test.robot in the directory
from which you ran the script. To change the directory where you
wish the result output to reside, add the ``-d`` flag:

.. code-block:: none

    $ pybot -d $RESULTS_DIR tests/test.robot

..

Tutorials
---------

+ `Configure Pycharm and Robot`_

+ `Intro to Robot and Examples`_


.. _Robot Framework: http://robotframework.org/
.. _builtin test: http://robotframework.org/#test-libraries
.. _external test: http://robotframework.org/#test-libraries
.. _intellibot: https://plugins.jetbrains.com/plugin/7386?pr=
.. _Selenium2Library: https://github.com/robotframework/Selenium2Library
.. _this one: https://www.youtube.com/watch?v=r3Mg60r1Jjk
.. _User Guide: http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html
.. _test library APIs: http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html#id208
.. _Configure Pycharm and Robot: https://www.youtube.com/watch?v=r3Mg60r1Jjk
.. _Intro to Robot and Examples: https://www.youtube.com/watch?v=CrkfmqFbJpU
