.. _introduction:

Introduction 
============

This chapter introduces the software development policies,
methodology for source code and configuration file management,
documentation, and use of continuous integration mechanisms
used for deployment and testing of DIMS components.

Overview
--------

+ Section :ref:`policy` discusses the policies that developers should
  understand in order to operate in a manner consistent with the rest of the
  team.

  .. TODO(mboggess):
  .. todo::

        I'm not sure this is true anymore. (E.g., :ref:`developingongithub`)

        .. note::

            Section :ref:`policy` is not very well developed. It needs to be
            completed or dropped.

        ..

  ..

+ All documentation for the DIMS project is written using restructured text
  (reST) and Sphinx. Section ref:`documentation` covers how to use these
  tools for producing professional looking and cross-referenced on-line (HTML)
  and off-line (PDF) documentation.

+ DIMS software is maintained under version control using Git and the HubFlow
  methodology and tool set. Section :ref:`sourcemanagement` covers how these
  tools are used for source code, documentation, and system configuration
  files.

+ Changes to source code that are pushed to Git repositories trigger build
  processes using the Jenkins continuous integration environment.  These triggers
  build and/or deploy software to specified locations, run tests, and/or
  configure service components. In most cases, Ansible is used as part of the
  process driven by Jenkins.  Section :ref:`continuousintegration` provides an
  overview of how this works and how to use it in development and testing DIMS
  components.

+ System software installation and configuration of DIMS components are managed
  using Ansible playbooks that are in turn maintained in Git repositories. Only
  a bare minimum of manual steps are required to bootstrap a DIMS deployment.
  After that, configuration changes are made to Git repositories and those
  changes trigger continuous integration processes to get these changes into
  the running system.  Section :ref:`deployconfigure` covers how to use this
  framework for adding or managing the open source components that are used
  in a DIMS deployment.

+ For more information about the Lifecycle of DIMS Machines reference 
  :ref:`dimspacker:lifecycle`. For more information about software development
  in general, reference :ref:`dittrich:swdev`.
