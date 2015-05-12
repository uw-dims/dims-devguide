.. _deployconfigure:

Deployment and Configuration
============================

Deployment and Configuration
----------------------------

.. todo::

   This section will describe how to do orchestration, or
   the orderly and systematic configruation of all microservice
   components that make up a DIMS deployment through a combination
   of Docker containers, virtual machines, and bare metal servers.
   This may involve merging Section :ref:`usingdocker` and
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

This refers to virtual machines (or a system of virtual machines), managed by Vagrant,
that a developer runs on his developer workstation to develop, debug and test code. 

.. _integrationenvironment:

Integration environment
~~~~~~~~~~~~~~~~~~~~~~~
