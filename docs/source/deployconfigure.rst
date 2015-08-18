.. _deployconfigure:

Deployment and Configuration
============================

Deployment and Configuration
----------------------------

.. todo::

   This section will describe how to do orchestration, or
   the orderly and systematic configuration of all microservice
   components that make up a DIMS deployment through a combination
   of Docker containers, virtual machines, and bare metal servers.
   This may involve merging Section :ref:`dimsdockerfiles:usingdocker` and
   Section :ref:`vmprovisioning`.

..

.. _systemtypes:

Type of Systems
---------------

This section will outline the general types of DIMS systems we will deploy. For example:

   #. Developer boxes
   #. Integration environment
   #. Dedicated test environment
   #. Production environment

These names were borrowed from the Hubflow documentation 
at http://datasift.github.io/gitflow/Versioning.html

.. _developerboxes:

Developer boxes
~~~~~~~~~~~~~~~

This refers to one of two types of systems:

#. Virtual machines (or a system of virtual machines), managed by Vagrant, that a
   developer runs on his developer workstation to develop, debug and test code.

#. A bare-metal system, such as a laptop, used for development, field testing,
   demonstrations, etc.


.. _integrationenvironment:

Integration environment
~~~~~~~~~~~~~~~~~~~~~~~
