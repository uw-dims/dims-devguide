.. DIMS Developer Guide  master file, created by
   copying from dims-ci-utils source tree on Sat May  9 14:44:33 PDT 2015.

.. _dimsdeveloperguide:

================================
DIMS Developer Guide v |release|
================================

This document (version |release|) covers issues related to development
of DIMS components from a developer's perspective. It is related closely
to, and frequently cross-references, the :ref:`dimsciutils:dimsciutilities`
(``dims-ci-utils`` for short).

.. warning::

   This is a `living document` that is being updated frequently to reflect
   current practices within the DIMS team.  As such, it may undergo
   restructuring frequently and the most up-to-date information will be found
   at https://u12-dev-svr-1.prisem.washington.edu:8443/docs/

   Throughout this document, ``warning::``, ``note::``, or other
   callout directives are used to let you know when to read carefully
   and be aware of things that might not be exactly up-to-date or
   safe for blind *cut+paste* into command lines.

..

.. toctree::
   :numbered:
   :maxdepth: 2

   introduction
   referenceddocs
   policy
   onboarding
   sourcemanagement
   documentation
   continuousintegration
   deployconfigure
   programming
   communication
   opstrustdb
   appendices

.. note::

        This document is written in `Restructured Text (reST) <http://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html>`_ using `Sphinx <http://sphinx-doc.org/>`_.

        For more information, see :ref:`documentation`.

..

Contact
-------

.. sectionauthor:: Dave Dittrich (@davedittrich) <dittrich @ u.washington.edu>

License
-------

.. include:: <isonum.txt>

Copyright |copy| 2014, 2015 University of Washington. All rights reserved.

.. include:: license.txt
   :literal:
