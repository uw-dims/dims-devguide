.. _dimscli:

Developing modules for the DIMS CLI app (``dimscli``)
-----------------------------------------------------

.. _bootstrappingdimscli:

Bootstrapping the ``dimscli`` app for development
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. TODO(dittrich): Added on
.. todo::

    .. note::

        This section is an initial effort to show how to bootstrap development
        of the ``dimscli`` app.

        Sun Nov  8 15:43:26 PST 2015

    ..

..


#. Clone the repo ``python-dimscli`` from ``git.prisem.washington.edu``. This can be
   done by running ``dims.git.syncrepos``:

#. Prepare a new Python virtual environment with all of the DIMS pre-requisite
   tools necessary for DIMS software development:

   .. code-block:: none

       [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $ VENV=dimscli dimsenv.install.user
       sudo password:

       PLAY [Install python virtual environment] *************************************

       ...

       PLAY RECAP ********************************************************************
       localhost                  : ok=30   changed=19   unreachable=0    failed=0

   ..

   The new ``dimscli`` virtual environment should show up as an option for
   ``workon``:
   
   .. code-block:: none

       [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $ workon
       dimscli
       dimsenv

   ..

#. Invoke the new ``dimscli`` Python virtual environment.

   .. code-block:: none

       [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $ workon dimscli
       [+++] Virtual environment 'dimscli' activated [ansible-playbooks v1.2.113]

   ..

#. Because this is a new Python virtual environment created with the DIMS
   build tools, it only has those Python packages defined in Ansible
   playbooks role ``python-virtualenv``.

   .. todo::

      Add an intersphinx link to the details about this Ansible role.

   ..

   The first time you try to run ``dimscli``, or any time that you change
   any of the pre-requisites used for programming ``dimscli`` modules,
   you must use ``pip`` to update and/or install the required
   packages. These will eventually be added to the defaults for the
   ``dimsenv`` standard virtual environment.

   .. code-block:: none

       [dimscli] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $ pip install -U -r requirements.txt
       Collecting pbr<2.0,>=1.4 (from -r requirements.txt (line 1))
       Using cached pbr-1.8.1-py2.py3-none-any.whl
       Collecting six>=1.9.0 (from -r requirements.txt (line 2))
       Using cached six-1.10.0-py2.py3-none-any.whl
       Requirement already up-to-date: Babel>=1.3 in /home/dittrich/dims/envs/dimscli/lib/python2.7/site-packages (from -r requirements.txt (line 3))
       Collecting cliff>=1.14.0 (from -r requirements.txt (line 4))
       Downloading cliff-1.15.0-py2-none-any.whl
       Collecting keystoneauth1>=1.0.0 (from -r requirements.txt (line 5))
       Downloading keystoneauth1-1.2.0-py2.py3-none-any.whl (149kB)
       100% |████████████████████████████████| 151kB 2.7MB/s
       Collecting os-client-config!=1.6.2,>=1.4.0 (from -r requirements.txt (line 6))
       Downloading os_client_config-1.10.1-py2.py3-none-any.whl (42kB)
       100% |████████████████████████████████| 45kB 6.0MB/s

       ...

       Running setup.py bdist_wheel for msgpack-python
       Stored in directory: /home/dittrich/.cache/pip/wheels/f3/97/a5/dd6e3b680de10b689464c44bc211239d1fe54bd296ff860897
       Running setup.py bdist_wheel for functools32
       Stored in directory: /home/dittrich/.cache/pip/wheels/38/c6/c7/ee17acd621120c302e25c2fa8b3a8b235d5d1137c6ab4c9728
       Successfully built simplejson warlock msgpack-python functools32
       Installing collected packages: msgpack-python, oslo.serialization, python-keystoneclient, simplejson,
       python-neutronclient, functools32, jsonschema, jsonpointer, jsonpatch, warlock, python-glanceclient,
       python-novaclient, python-cinderclient, python-openstackclient

       Successfully installed functools32-3.2.3.post2 jsonpatch-1.12 jsonpointer-1.10 jsonschema-2.5.1 msgpack-python-0.4.6
       oslo.serialization-1.11.0 python-cinderclient-1.4.0 python-glanceclient-1.1.0 python-keystoneclient-1.8.1
       python-neutronclient-3.1.0 python-novaclient-2.34.0 python-openstackclient-1.8.0 simplejson-3.8.1 warlock-1.2.0
       PrettyTable-0.7.2 appdirs-1.4.0 cliff-1.15.0 cliff-tablib-1.1 cmd2-0.6.8 debtcollector-0.10.0 iso8601-0.1.11
       keystoneauth1-1.2.0 monotonic-0.4 netaddr-0.7.18 netifaces-0.10.4 os-client-config-1.10.1 oslo.config-2.6.0
       oslo.i18n-2.7.0 oslo.utils-2.7.0 oslosphinx-3.3.1 pbr-1.8.1 pyparsing-2.0.5 pytz-2015.7 requests-2.8.1
       six-1.10.0 stevedore-1.9.0 tablib-0.10.0 unicodecsv-0.14.1 wrapt-1.10.5

   ..

#. Once all the pre-requisite packages are installed in the virtual environment,
   install the ``dimscli`` app and its modules as well using ``python setup.py
   install`` or ``pip install -e .`` (either will work):

   .. code-block:: none

       [dimscli] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $ python setup.py install
       running install
       [pbr] Writing ChangeLog
       [pbr] Generating ChangeLog
       [pbr] ChangeLog complete (0.0s)
       [pbr] Generating AUTHORS
       [pbr] AUTHORS complete (0.0s)
       running build
       running build_py
       creating build
       creating build/lib
       creating build/lib/dimscli
       creating build/lib/dimscli/common

       ...

       byte-compiling /home/dittrich/dims/envs/dimscli/lib/python2.7/site-packages/dimscli/common/timing.py to timing.pyc
       byte-compiling /home/dittrich/dims/envs/dimscli/lib/python2.7/site-packages/dimscli/common/context.py to context.pyc
       byte-compiling /home/dittrich/dims/envs/dimscli/lib/python2.7/site-packages/dimscli/common/clientmanager.py to clientmanager.pyc
       byte-compiling /home/dittrich/dims/envs/dimscli/lib/python2.7/site-packages/dimscli/common/logs.py to logs.pyc
       byte-compiling /home/dittrich/dims/envs/dimscli/lib/python2.7/site-packages/dimscli/common/utils.py to utils.pyc
       running install_egg_info
       Copying python_dimscli.egg-info to /home/dittrich/dims/envs/dimscli/lib/python2.7/site-packages/python_dimscli-0.0.1.dev391-py2.7.egg-info
       running install_scripts
       Installing dimscli script to /home/dittrich/dims/envs/dimscli/bin

   ..

#. Run the ``dimscli`` app like any other program, directly from the command line.

   There are two ways to use ``dimscli``.

   * As a single command with command line options like other Linux commands


     .. code-block:: none

         [dimscli] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $ dimscli --version
         dimscli 0.0.1
         [dimscli] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $

     ..

   * As an interactive shell that allows you to run multiple commands in
     sequence within the same context (i.e., the same state, or runtime settings
     you invoke while in the shell) by just just the program name and no
     arguments or options.

     .. code-block:: none
   
          [dimscli] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $ dimscli
          defaults: {u'auth_type': 'password', u'compute_api_version': u'2', 'key': None, u'database_api_version': u'1.0',
          'api_timeout': None, u'baremetal_api_version': u'1', 'cacert': None, u'image_api_use_tasks': False,
          u'floating_ip_source': u'neutron', u'orchestration_api_version': u'1', u'interface': None, u'network_api_version':
          u'2.0', u'image_format': u'qcow2', u'object_api_version': u'1', u'image_api_version': u'2', 'verify': True,
          u'identity_api_version': u'2.0', u'volume_api_version': u'1', 'cert': None, u'secgroup_source': u'neutron',
          u'dns_api_version': u'2', u'disable_vendor_agent': {}}
          cloud cfg: {'auth_type': 'password', u'compute_api_version': u'2', u'orchestration_api_version': u'1',
          u'database_api_version': u'1.0', 'cacert': None, u'network_api_version': u'2.0', u'image_format': u'qcow2',
          u'object_api_version': u'1', u'image_api_version': u'2', 'verify': True, u'dns_api_version': u'2',
          'verbose_level': '1', 'region_name': '', 'api_timeout': None, u'baremetal_api_version': u'1', 'auth': {},
          'default_domain': 'default', u'image_api_use_tasks': False, u'floating_ip_source': u'neutron', 'key': None,
          'timing': False, 'deferred_help': False, u'identity_api_version': u'2.0', u'volume_api_version': u'1',
          'cert': None, u'secgroup_source': u'neutron', u'interface': None, u'disable_vendor_agent': {}}
          compute API version 2, cmd group dims.compute.v2
          network version 2.0 is not in supported versions 2
          network API version 2.0, cmd group dims.network.v2
          image API version 2, cmd group dims.image.v2
          volume API version 1, cmd group dims.volume.v1
          identity API version 2.0, cmd group dims.identity.v2
          object_store API version 1, cmd group dims.object_store.v1
          (dimscli) help
      
          Shell commands (type help <topic>):
          ===================================
          cmdenvironment  edit  hi       l   list  pause  r    save  shell      show
          ed              help  history  li  load  py     run  set   shortcuts
      
          Undocumented commands:
          ======================
          EOF  eof  exit  q  quit
      
          Application commands (type help <topic>):
          =========================================
          aggregate add host     host show              role list
          aggregate create       ip fixed add           role remove
          aggregate delete       ip fixed remove        role show
          aggregate list         ip floating add        security group create
          aggregate remove host  ip floating create     security group delete
          aggregate set          ip floating delete     security group list
          aggregate show         ip floating list       security group rule create
          catalog list           ip floating pool list  security group rule delete
          catalog show           ip floating remove     security group rule list
          command list           keypair create         security group set
          complete               keypair delete         security group show
          configuration show     keypair list           server create
          console log show       keypair show           server delete
          console url show       module list            server image create
          container create       network create         server list
          container delete       network delete         server reboot
          container list         network list           server rebuild
          container save         network set            server set
          container show         network show           server show
          endpoint create        object create          server ssh
          endpoint delete        object delete          service create
          endpoint list          object list            service delete
          endpoint show          object save            service list
          extension list         object show            service show
          flavor create          project create         token issue
          flavor delete          project delete         token revoke
          flavor list            project list           user create
          flavor set             project set            user delete
          flavor show            project show           user list
          flavor unset           role add               user role list
          help                   role create            user set
          host list              role delete            user show
      
          (dimscli) exit
          END return value: 0
          [dimscli] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $
   
     ..

.. attention::

    See the file ``$GIT/python-dimscli/README.rst`` for more
    documentation produced during initial creation of the openstackclient
    fork of ``dimscli``.

    .. todo::

        Make this an intersphinx link as soon as documentation auto-generation
        is working for that repo.

    ..

..

