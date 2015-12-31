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


.. _completingdimscli:

Completing commands in ``dimscli``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The initial implementation of ``dimscli`` ported from the ``openstacklient``
code base does not have much actual code underlying it, though the
scaffolding of ``openstacklient`` and many of its defined modules are
currently configured in the code. You can see the modules that are
not there by simply asking for ``dimscli --help`` and noting the
errors (and what they point to, which indicates which code you
need to seek out to use and/or replace.)

.. code-block:: none

    [dimscli] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $ dimscli --help
    defaults: {u'auth_type': 'password', u'compute_api_version': u'2', 'key': None, u'database_api_version': u'1.0', 'api_timeout': None, u'baremetal_api_version': u'1', 'cacert': None, u'image_api_use_tasks
    ': False, u'floating_ip_source': u'neutron', u'orchestration_api_version': u'1', u'interface': None, u'network_api_version': u'2.0', u'image_format': u'qcow2', u'object_api_version': u'1', u'image_api_ve
    rsion': u'2', 'verify': True, u'identity_api_version': u'2.0', u'volume_api_version': u'1', 'cert': None, u'secgroup_source': u'neutron', u'dns_api_version': u'2', u'disable_vendor_agent': {}}
    cloud cfg: {'auth_type': 'password', u'compute_api_version': u'2', u'orchestration_api_version': u'1', u'database_api_version': u'1.0', 'cacert': None, u'network_api_version': u'2.0', u'image_format': u'
    qcow2', u'object_api_version': u'1', u'image_api_version': u'2', 'verify': True, u'dns_api_version': u'2', 'verbose_level': '1', 'region_name': '', 'api_timeout': None, u'baremetal_api_version': u'1', 'a
    uth': {}, 'default_domain': 'default', u'image_api_use_tasks': False, u'floating_ip_source': u'neutron', 'key': None, 'timing': False, 'deferred_help': True, u'identity_api_version': u'2.0', u'volume_api
    _version': u'1', 'cert': None, u'secgroup_source': u'neutron', u'interface': None, u'disable_vendor_agent': {}}
    compute API version 2, cmd group dims.compute.v2
    network version 2.0 is not in supported versions 2
    network API version 2.0, cmd group dims.network.v2
    image API version 2, cmd group dims.image.v2
    volume API version 1, cmd group dims.volume.v1
    identity API version 2.0, cmd group dims.identity.v2
    object_store API version 1, cmd group dims.object_store.v1
    usage: dimscli [--version] [-v] [--log-file LOG_FILE] [-q] [-h] [--debug]
                   [--os-cloud <cloud-config-name>]
                   [--os-region-name <auth-region-name>]
                   [--os-cacert <ca-bundle-file>] [--verify | --insecure]
                   [--os-default-domain <auth-domain>]
     ...

      --os-object-api-version <object-api-version>
                            Object API version, default=1 (Env:
                            OS_OBJECT_API_VERSION)

    Commands:
    Could not load EntryPoint.parse('aggregate_add_host = dimscli.compute.v2.aggregate:AddAggregateHost')
    Could not load EntryPoint.parse('aggregate_create = dimscli.compute.v2.aggregate:CreateAggregate')
    Could not load EntryPoint.parse('aggregate_delete = dimscli.compute.v2.aggregate:DeleteAggregate')
    Could not load EntryPoint.parse('aggregate_list = dimscli.compute.v2.aggregate:ListAggregate')
    Could not load EntryPoint.parse('aggregate_remove_host = dimscli.compute.v2.aggregate:RemoveAggregateHost')
    Could not load EntryPoint.parse('aggregate_set = dimscli.compute.v2.aggregate:SetAggregate')
    Could not load EntryPoint.parse('aggregate_show = dimscli.compute.v2.aggregate:ShowAggregate')
    Could not load EntryPoint.parse('catalog_list = dimscli.identity.v2_0.catalog:ListCatalog')
    Could not load EntryPoint.parse('catalog_show = dimscli.identity.v2_0.catalog:ShowCatalog')
    Could not load EntryPoint.parse('command_list = dimscli.common.module:ListCommand')
      complete       print bash completion command
    Could not load EntryPoint.parse('configuration_show = dimscli.common.configuration:ShowConfiguration')
    Could not load EntryPoint.parse('console_log_show = dimscli.compute.v2.console:ShowConsoleLog')
    Could not load EntryPoint.parse('console_url_show = dimscli.compute.v2.console:ShowConsoleURL')
    Could not load EntryPoint.parse('container_create = dimscli.object.v1.container:CreateContainer')
    Could not load EntryPoint.parse('container_delete = dimscli.object.v1.container:DeleteContainer')
    Could not load EntryPoint.parse('container_list = dimscli.object.v1.container:ListContainer')
    Could not load EntryPoint.parse('container_save = dimscli.object.v1.container:SaveContainer')
    Could not load EntryPoint.parse('container_show = dimscli.object.v1.container:ShowContainer')
    Could not load EntryPoint.parse('endpoint_create = dimscli.identity.v2_0.endpoint:CreateEndpoint')
    Could not load EntryPoint.parse('endpoint_delete = dimscli.identity.v2_0.endpoint:DeleteEndpoint')
    Could not load EntryPoint.parse('endpoint_list = dimscli.identity.v2_0.endpoint:ListEndpoint')
    Could not load EntryPoint.parse('endpoint_show = dimscli.identity.v2_0.endpoint:ShowEndpoint')
    Could not load EntryPoint.parse('extension_list = dimscli.common.extension:ListExtension')
    Could not load EntryPoint.parse('flavor_create = dimscli.compute.v2.flavor:CreateFlavor')
    Could not load EntryPoint.parse('flavor_delete = dimscli.compute.v2.flavor:DeleteFlavor')
    Could not load EntryPoint.parse('flavor_list = dimscli.compute.v2.flavor:ListFlavor')
    Could not load EntryPoint.parse('flavor_set = dimscli.compute.v2.flavor:SetFlavor')
    Could not load EntryPoint.parse('flavor_show = dimscli.compute.v2.flavor:ShowFlavor')
    Could not load EntryPoint.parse('flavor_unset = dimscli.compute.v2.flavor:UnsetFlavor')
      help           print detailed help for another command
    Could not load EntryPoint.parse('host_list = dimscli.compute.v2.host:ListHost')
    Could not load EntryPoint.parse('host_show = dimscli.compute.v2.host:ShowHost')
    Could not load EntryPoint.parse('ip_fixed_add = dimscli.compute.v2.fixedip:AddFixedIP')
    Could not load EntryPoint.parse('ip_fixed_remove = dimscli.compute.v2.fixedip:RemoveFixedIP')
    Could not load EntryPoint.parse('ip_floating_add = dimscli.compute.v2.floatingip:AddFloatingIP')
    Could not load EntryPoint.parse('ip_floating_create = dimscli.compute.v2.floatingip:CreateFloatingIP')
    Could not load EntryPoint.parse('ip_floating_delete = dimscli.compute.v2.floatingip:DeleteFloatingIP')
    Could not load EntryPoint.parse('ip_floating_list = dimscli.compute.v2.floatingip:ListFloatingIP')
    Could not load EntryPoint.parse('ip_floating_pool_list = dimscli.compute.v2.floatingippool:ListFloatingIPPool')
    Could not load EntryPoint.parse('ip_floating_remove = dimscli.compute.v2.floatingip:RemoveFloatingIP')
    Could not load EntryPoint.parse('keypair_create = dimscli.compute.v2.keypair:CreateKeypair')
    Could not load EntryPoint.parse('keypair_delete = dimscli.compute.v2.keypair:DeleteKeypair')
    Could not load EntryPoint.parse('keypair_list = dimscli.compute.v2.keypair:ListKeypair')
    Could not load EntryPoint.parse('keypair_show = dimscli.compute.v2.keypair:ShowKeypair')
    Could not load EntryPoint.parse('module_list = dimscli.common.module:ListModule')
    Could not load EntryPoint.parse('network_create = dimscli.network.v2.network:CreateNetwork')
    Could not load EntryPoint.parse('network_delete = dimscli.network.v2.network:DeleteNetwork')
    Could not load EntryPoint.parse('network_list = dimscli.network.v2.network:ListNetwork')
    Could not load EntryPoint.parse('network_set = dimscli.network.v2.network:SetNetwork')
    Could not load EntryPoint.parse('network_show = dimscli.network.v2.network:ShowNetwork')
    Could not load EntryPoint.parse('object_create = dimscli.object.v1.object:CreateObject')
    Could not load EntryPoint.parse('object_delete = dimscli.object.v1.object:DeleteObject')
    Could not load EntryPoint.parse('object_list = dimscli.object.v1.object:ListObject')
    Could not load EntryPoint.parse('object_save = dimscli.object.v1.object:SaveObject')
    Could not load EntryPoint.parse('object_show = dimscli.object.v1.object:ShowObject')
    Could not load EntryPoint.parse('project_create = dimscli.identity.v2_0.project:CreateProject')
    Could not load EntryPoint.parse('project_delete = dimscli.identity.v2_0.project:DeleteProject')
    Could not load EntryPoint.parse('project_list = dimscli.identity.v2_0.project:ListProject')
    Could not load EntryPoint.parse('project_set = dimscli.identity.v2_0.project:SetProject')
    Could not load EntryPoint.parse('project_show = dimscli.identity.v2_0.project:ShowProject')
    Could not load EntryPoint.parse('role_add = dimscli.identity.v2_0.role:AddRole')
    Could not load EntryPoint.parse('role_create = dimscli.identity.v2_0.role:CreateRole')
    Could not load EntryPoint.parse('role_delete = dimscli.identity.v2_0.role:DeleteRole')
    Could not load EntryPoint.parse('role_list = dimscli.identity.v2_0.role:ListRole')
    Could not load EntryPoint.parse('role_remove = dimscli.identity.v2_0.role:RemoveRole')
    Could not load EntryPoint.parse('role_show = dimscli.identity.v2_0.role:ShowRole')
    Could not load EntryPoint.parse('security_group_create = dimscli.compute.v2.security_group:CreateSecurityGroup')
    Could not load EntryPoint.parse('security_group_delete = dimscli.compute.v2.security_group:DeleteSecurityGroup')
    Could not load EntryPoint.parse('security_group_list = dimscli.compute.v2.security_group:ListSecurityGroup')
    Could not load EntryPoint.parse('security_group_rule_create = dimscli.compute.v2.security_group:CreateSecurityGroupRule')
    Could not load EntryPoint.parse('security_group_rule_delete = dimscli.compute.v2.security_group:DeleteSecurityGroupRule')
    Could not load EntryPoint.parse('security_group_rule_list = dimscli.compute.v2.security_group:ListSecurityGroupRule')
    Could not load EntryPoint.parse('security_group_set = dimscli.compute.v2.security_group:SetSecurityGroup')
    Could not load EntryPoint.parse('security_group_show = dimscli.compute.v2.security_group:ShowSecurityGroup')
    Could not load EntryPoint.parse('server_create = dimscli.compute.v2.server:CreateServer')
    Could not load EntryPoint.parse('server_delete = dimscli.compute.v2.server:DeleteServer')
    Could not load EntryPoint.parse('server_image_create = dimscli.compute.v2.server:CreateServerImage')
    Could not load EntryPoint.parse('server_list = dimscli.compute.v2.server:ListServer')
    Could not load EntryPoint.parse('server_reboot = dimscli.compute.v2.server:RebootServer')
    Could not load EntryPoint.parse('server_rebuild = dimscli.compute.v2.server:RebuildServer')
    Could not load EntryPoint.parse('server_set = dimscli.compute.v2.server:SetServer')
    Could not load EntryPoint.parse('server_show = dimscli.compute.v2.server:ShowServer')
    Could not load EntryPoint.parse('server_ssh = dimscli.compute.v2.server:SshServer')
    Could not load EntryPoint.parse('service_create = dimscli.identity.v2_0.service:CreateService')
    Could not load EntryPoint.parse('service_delete = dimscli.identity.v2_0.service:DeleteService')
    Could not load EntryPoint.parse('service_list = dimscli.identity.v2_0.service:ListService')
    Could not load EntryPoint.parse('service_show = dimscli.identity.v2_0.service:ShowService')
    Could not load EntryPoint.parse('token_issue = dimscli.identity.v2_0.token:IssueToken')
    Could not load EntryPoint.parse('token_revoke = dimscli.identity.v2_0.token:RevokeToken')
    Could not load EntryPoint.parse('user_create = dimscli.identity.v2_0.user:CreateUser')
    Could not load EntryPoint.parse('user_delete = dimscli.identity.v2_0.user:DeleteUser')
    Could not load EntryPoint.parse('user_list = dimscli.identity.v2_0.user:ListUser')
    Could not load EntryPoint.parse('user_role_list = dimscli.identity.v2_0.role:ListUserRole')
    Could not load EntryPoint.parse('user_set = dimscli.identity.v2_0.user:SetUser')
    Could not load EntryPoint.parse('user_show = dimscli.identity.v2_0.user:ShowUser')
    END return value: 1
    [dimscli] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop) $

..

Using the last error message above as an example, there needs to be a module
named ``$GIT/python-dimscli/dimscli/identity/v2_0/user.py`` with a
class ``ShowUser``. Look in the ``python-openstack/openstack/identity/v2_0/``
directory for their ``user.py`` and build off that example.

.. attention::

    Clone the ``python-openstackclient`` repo using ``git clone
    https://git.openstack.org/openstack/python-openstackclient`` and
    see the ``cliff`` documentation, Section `Exploring the Demo App`_, for how
    this works.

..

.. _Exploring the Demo App: http://docs.openstack.org/developer/cliff/demoapp.html

.. attention::

    See the file ``$GIT/python-dimscli/README.rst`` for more
    documentation produced during initial creation of the openstackclient
    fork of ``dimscli``.

    .. todo::

        Make this an intersphinx link as soon as documentation auto-generation
        is working for that repo.

    ..

..

``cliff`` supports list formatting in tables, CSV, JSON, etc., but not in shell
format. That is only supported by the ``ShowOne`` class, which is not what we
want for producing a set of variables for insertion into shell environments.

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop*) $ dimscli list nodes
    +----------------+---------------+
    | Node           | Address       |
    +----------------+---------------+
    | b52            | 10.86.86.7    |
    | consul-breathe | 10.142.29.117 |
    | consul-echoes  | 10.142.29.116 |
    | consul-seamus  | 10.142.29.120 |
    | dimsdemo1      | 10.86.86.2    |
    | dimsdev1       | 10.86.86.5    |
    | dimsdev2       | 10.86.86.5    |
    | four           | 192.168.0.101 |
    +----------------+---------------+

..

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop*) $ dimscli list nodes -f csv
    "Node","Address"
    "b52","10.86.86.7"
    "consul-breathe","10.142.29.117"
    "consul-echoes","10.142.29.116"
    "consul-seamus","10.142.29.120"
    "dimsdemo1","10.86.86.2"
    "dimsdev1","10.86.86.5"
    "dimsdev2","10.86.86.5"
    "four","192.168.0.101"

..


.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop*) $ dimscli list nodes -f json
    [{"Node": "b52", "Address": "10.86.86.7"}, {"Node": "consul-breathe", "Address": "10.142.29.117"}, {"Node": "consul-echoes", "Address": "10.142.29.116"}, {"Node": "consul-seamus", "Address": "10.142.29.120"}, {"Node": "dimsdemo1", "Address": "10.86.86.2"}, {"Node": "dimsdev1", "Address": "10.86.86.5"}, {"Node": "dimsdev2", "Address": "10.86.86.5"}, {"Node": "four", "Address": "192.168.0.101"}]
    [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop*) $ dimscli list nodes -f json | python -m json.tool
    [
        {
            "Address": "10.86.86.7",
            "Node": "b52"
        },
        {
            "Address": "10.142.29.117",
            "Node": "consul-breathe"
        },
        {
            "Address": "10.142.29.116",
            "Node": "consul-echoes"
        },
        {
            "Address": "10.142.29.120",
            "Node": "consul-seamus"
        },
        {
            "Address": "10.86.86.2",
            "Node": "dimsdemo1"
        },
        {
            "Address": "10.86.86.5",
            "Node": "dimsdev1"
        },
        {
            "Address": "10.86.86.5",
            "Node": "dimsdev2"
        },
        {
            "Address": "192.168.0.101",
            "Node": "four"
        }
    ]

..


To produce the list in the form of shell variables, we need to create a custom
formatter and load it into the ``dimscli`` shell via Stevedore.

.. TODO(dittrich): Integrate a description of Git commit 30d096e4f2b25a37fb5eae4dfe9420b0e3960757

.. todo::

    Integrate a description of this Git commit:

    .. code-block:: git

        commit 30d096e4f2b25a37fb5eae4dfe9420b0e3960757
        Author: Dave Dittrich <dittrich@u.washington.edu>
        Date:   Wed Dec 30 23:25:19 2015 -0800

            Add initial lister with custom shell formatter for listing Consul nodes

        diff --git a/dimscli/formatters/shell.py b/dimscli/formatters/shell.py
        new file mode 100644
        index 0000000..a9c86d7
        --- /dev/null
        +++ b/dimscli/formatters/shell.py
        @@ -0,0 +1,50 @@
        +"""Output formatters using shell syntax.
        +"""
        +
        +from cliff.formatters.base import ListFormatter, SingleFormatter
        +
        +import argparse
        +import six
        +
        +
        +class DIMSShellFormatter(ListFormatter, SingleFormatter):
        +
        +    def add_argument_group(self, parser):
        +        group = parser.add_argument_group(
        +            title='shell formatter',
        +            description='a format a UNIX shell can parse (variable="value")',
        +        )
        +        group.add_argument(
        +            '--variable',
        +            action='append',
        +            default=[],
        +            dest='variables',
        +            metavar='VARIABLE',
        +            help=argparse.SUPPRESS,
        +        )
        +        group.add_argument(
        +            '--prefix',
        +            action='store',
        +            default='',
        +            dest='prefix',
        +            help='add a prefix to all variable names',
        +        )
        +
        +    def emit_one(self, column_names, data, stdout, parsed_args):
        +        variable_names = [c.lower().replace(' ', '_')
        +                          for c in column_names
        +                          ]
        +        desired_columns = parsed_args.variables
        +        for name, value in zip(variable_names, data):
        +            if name in desired_columns or not desired_columns:
        +                if isinstance(value, six.string_types):
        +                    value = value.replace('"', '\\"')
        +                stdout.write('%s%s="%s"\n' % (parsed_args.prefix, name, value))
        +        return
        +
        +    def emit_list(self, column_names, data, stdout, parsed_args):
        +        for name, value in data:
        +            if isinstance(value, six.string_types):
        +                value = value.replace('"', '\\"')
        +            stdout.write('%s%s="%s"\n' % (parsed_args.prefix, name, value))
        +        return
        \ No newline at end of file
        diff --git a/dimscli/list.py b/dimscli/list.py
        index 3dcee4a..45acdda 100644
        --- a/dimscli/list.py
        +++ b/dimscli/list.py
        @@ -1,8 +1,10 @@
         import logging
         import os
        +import consulate
        +import json

         from cliff.lister import Lister
        -
        +from cliff.show import ShowOne

         class Files(Lister):
             """Show a list of files in the current directory.
        @@ -16,3 +18,17 @@ class Files(Lister):
                 return (('Name', 'Size'),
                         ((n, os.stat(n).st_size) for n in os.listdir('.'))
                         )
        +
        +class Nodes(Lister):
        +    """Show a list of nodes registered in Consul.
        +
        +    """
        +
        +    log = logging.getLogger(__name__)
        +
        +    def take_action(self, parsed_args):
        +        consul = consulate.Consul()
        +        nodes = consul.catalog.nodes()
        +        columns = ('Node', 'Address')
        +        data = ((node['Node'], node['Address']) for node in nodes)
        +        return (columns, data)
        diff --git a/requirements.txt b/requirements.txt
        index b74529f..7c54111 100644
        --- a/requirements.txt
        +++ b/requirements.txt
        @@ -22,3 +22,4 @@ stevedore>=1.5.0 # Apache-2.0
         unicodecsv>=0.8.0
         PyYAML>=3.1.0
         python-openstackclient>=1.8.0
        +consulate==0.6.0
        diff --git a/setup.cfg b/setup.cfg
        index f869411..2f17c6c 100644
        --- a/setup.cfg
        +++ b/setup.cfg
        @@ -35,9 +35,16 @@ dims.cli =
             command_list = dimscli.common.module:ListCommand
             module_list = dimscli.common.module:ListModule
             list_files = dimscli.list:Files
        +    list_nodes = dimscli.list:Nodes
             files = dimscli.list:Files
             show_file = dimscli.show:File

        +cliff.formatter.list =
        +    shell = dimscli.formatters.shell:DIMSShellFormatter
        +
        +cliff.formatter.show =
        +    shell = dimscli.formatters.shell:DIMSShellFormatter
        +
         dims.cli.base =
             compute = dimscli.compute.client
         #   identity = dimscli.identity.client

    ..

..

After adding the new formatter, it is possible to extract the list of nodes registered
with Consul and produce a set of variable declarations from the list.

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop*) $ dimscli list nodes -f shell
    b52="10.86.86.7"
    consul_breathe="10.142.29.117"
    consul_echoes="10.142.29.116"
    consul_seamus="10.142.29.120"
    dimsdemo1="10.86.86.2"
    dimsdev1="10.86.86.5"
    dimsdev2="10.86.86.5"
    four="192.168.0.101"

..

In practice, you may wish to insert these as variables in the shell's ``set`` using
the ``eval`` statement for use when invoking shell commands:

.. code-block:: none

    [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop*) $ eval $(dimscli list nodes -f shell --prefix=DIMS_)
    [dimsenv] dittrich@dimsdemo1:~/dims/git/python-dimscli (develop*) $ set | grep DIMS_
    DIMS_REV=unspecified
    DIMS_VERSION='1.6.124 (dims-ci-utils)'
    DIMS_b52=10.86.86.7
    DIMS_consul_breathe=10.142.29.117
    DIMS_consul_echoes=10.142.29.116
    DIMS_consul_seamus=10.142.29.120
    DIMS_dimsdemo1=10.86.86.2
    DIMS_dimsdev2=10.86.86.5
    DIMS_four=192.168.0.101
        echo "REV:     $DIMS_REV";
        echo "[dims-ci-utils version $(version) (rev $DIMS_REV)]";
            echo "$PROGRAM $DIMS_VERSION";
            echo "$BASE $DIMS_VERSION";

..

