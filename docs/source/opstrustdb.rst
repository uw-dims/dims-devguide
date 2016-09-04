.. _opstrustdb:

Ops-trust-db VM Creation
========================

These are instructions for running Ansible playbooks \to install a clone of the current ops-trust database. You first need a VM to install it on. The directions for creating a VM are located at :ref:`dimspacker:vmquickstart`.

If you have already created a server .box file, then you can skip the first part of the instructions. You can either go to :ref:`dimspacker:vmquickstartinstantiate` and create a new server, or if you’ve already created one and want to re-use it, you can destroy it (via vagrant destroy) and bring it back up again fresh via vagrant up.

Section :ref:`dimspacker:vmquickstartrunansible` talks about running playbooks against the VM. 

.. TODO(mboggess):
.. todo::

    Point back to the best way to run playbooks against a host. It is no
    longer the script "run_playbook", as that does not exist anymore.
    See todo block at the beginning of :ref:`ansibleplaybooks:runplaybooksoverview`.

    This section can be kept for historical purposes or cleaned up.
..

For using the VM as a postgresql server, you don’t need to install the python virtual environment. Just run the following to prepare the VM:

.. code-block:: none

	$ ./run_playbook base-os.yml
	$ ./run_playbook common.yml
	$ ./run_playbook provision-dims-users.yml
	$ ./run_playbook dims-ci-utils-deploy.yml -vv -e artifact_branch=develop

..

.. TODO(mboggess):
.. todo::

    Point back to the best way to run playbooks against a host. It is no
    longer the script "run_playbook", as that does not exist anymore.
    See todo block at the beginning of :ref:`ansibleplaybooks:runplaybooksoverview`.

    This section can be kept for historical purposes or cleaned up.
..

Then, run the ops-trust-testdb-configure.yml playbook:

``$ ./run_playbook -g ops-trust-testdb-servers ops-trust-testdb-configure.yml -vv``

This will install postgresql, create the database, and populate it (if it doesn’t already exist). To re-initialize the database (like if you’ve been working with it and want to start over) you need to specify initialize_postgres_dims as true:

``$ ./run_playbook -g ops-trust-testdb-servers ops-trust-testdb-configure.yml -vv -e initialize_postgres_dims=true``

.. note::

	The following is an example of the data that exists in the ops-trust table.


	The data for ops-trust has been redacted and truncated.  Sensitive information has been replaced with * and only the first record of each table has been shown.  In addition some tables are not shown.

..

.. code-block:: none

	[('openid_source_cache',), ('second_factors',), ('web_sessions',), ('trustgroup',), ('attestations',), ('member_trustgroup',), ('language_skill',), ('audit_history',), ('languages',), ('member_mailinglist',), ('mailinglist',), ('member',), ('member_email',), ('member_detail_types',), ('member_details',), ('member_language_skill',), ('member_state',), ('member_vouch',), ('message_catalog',), ('message_types',), ('openid_associations',), ('second_factor_types',)]

	Executing SELECT on openid_source_cache
	Column names: ['src_ip', 'last_try', 'attempt_count']
	Number of records: 7
	First record: ('*.*.*.*', datetime.datetime(yyyy, mm, dd, hh, mm, ss, 159691), *)

	Executing SELECT on second_factors
	Column names: ['uuid', 'member', 'type', 'entered', 'active', 'counter', 'key', 'descr']
	Number of records: 0

	Executing SELECT on web_sessions
	Column names: ['id', 'a_session']
	Number of records: 59
	First record: ('******', "$D = {'form_id' => '******','test' => '******','uuid' => '******','sysadmin' => ******,'_SESSION_ETIME' => ******,'_SESSION_ID' => '******','admin' => ******,'ntg' => ******,'member' => '******','change_pw' => 0,'can_see' => 1,'~logged-in' => 't','trustgroup' => 'dims','_SESSION_REMOTE_ADDR' => '******.******.******.******','_SESSION_CTIME' => ******,'_catalyst_session' => {},'_SESSION_ATIME' => ******};;$D")

	Executing SELECT on trustgroup
	Column names: ['ident', 'descr', 'shortname', 'min_invouch', 'target_invouch', 'pgp_required', 'please_vouch', 'vouch_adminonly', 'nom_enabled', 'min_outvouch', 'max_inactivity', 'can_time_out', 'max_vouchdays', 'idle_guard', 'has_wiki']
	Number of records: 2
	First record: ('dims', 'DIMS Trust Group', 'dims', 1, 1, False, False, False, True, 0, datetime.timedelta(30), True, 15, datetime.timedelta(25), True)

	Executing SELECT on attestations
	Column names: ['ident', 'descr', 'trustgroup']
	Number of records: 6
	First record: ('met', 'I have met them in person more than once.', 'main')

	Executing SELECT on member_trustgroup
	Column names: ['member', 'trustgroup', 'admin', 'entered', 'activity', 'state', 'email']
	Number of records: 13
	First record: ('******', 'main', False, datetime.datetime(yyyy, mm, dd, hh, mm, ss, 275385), datetime.datetime(yyyy, mm, dd, hh, mm, ss, 944053), 'active', '******@uw.edu')

	Executing SELECT on language_skill
	Column names: ['skill', 'seq']
	Number of records: 4
	First record: ('native', 4)

	Executing SELECT on audit_history
	Column names: ['member', 'what', 'entered']
	Number of records: 259
	First record: ('******', "SQL: UPDATE member_trustgroup SET state = 'approved' WHERE ROW(member, trustgroup, state) = ROW('******', 'dims', 'vetted'); ", datetime.datetime(yyyy, mm, dd, hh, mm, ss, 135660))

	Executing SELECT on languages
	Column names: ['name', 'iso_639_1']
	Number of records: 184
	First record: ('Afar', 'aa')

	Executing SELECT on member_mailinglist
	Column names: ['member', 'lhs', 'trustgroup', 'virtual']
	Number of records: 70
	First record: ('******', 'general', 'dims', False)

	Executing SELECT on mailinglist
	Column names: ['lhs', 'trustgroup', 'descr', 'members_only', 'can_add_self', 'automatic', 'always_crypt', 'virtual', 'activity', 'email_footer', 'pubkey', 'key_update_at', 'seckey']
	Number of records: 12
	First record: ('vetting', 'dims', 'Vetting and Vouching', True, True, True, False, False, None, None, '-----BEGIN PGP PUBLIC KEY BLOCK-----\n******\n-----END PGP PUBLIC KEY BLOCK-----\n', datetime.datetime(yyyy, mm, dd, hh, mm, ss, 252368), '-----BEGIN PGP PRIVATE KEY BLOCK-----\n******\n-----END PGP PRIVATE KEY BLOCK-----\n')

	Executing SELECT on member
	Column names: ['ident', 'descr', 'affiliation', 'password', 'passwd_chat', 'tz_info', 'im_info', 'tel_info', 'sms_info', 'post_info', 'bio_info', 'airport', 'no_email', 'hide_email', 'furlough', 'change_pw', 'entered', 'activity', 'uuid', 'sysadmin', 'login_attempts', 'login_try_begin', 'image']
	Number of records: 8
	First record: ('******', '****** ******(Full name)', '@uw.edu', '******', None, '', '', '', None, '', '******', None, False, False, False, False, datetime.datetime(yyyy, mm, dd, hh, mm, ss, 488278), datetime.datetime(yyyy, mm, dd, hh, mm, ss, 288486), '0878ca30-c16a-435d-8991-39dd366fa4d4', False, 0, None, None)

	Executing SELECT on member_email
	Column names: ['member', 'email', 'pgpkey_id', 'verified', 'pgpkey_expire', 'keyring', 'keyring_update_at']
	Number of records: 8
	First record: ('*****', '*****@uw.edu', '8E01820D', True, datetime.datetime(yyyy, mm, dd, hh, mm, ss, 0), '-----BEGIN PGP PUBLIC KEY BLOCK-----\******\n-----END PGP PUBLIC KEY BLOCK-----\n', datetime.datetime(yyyy, mm, dd, hh, mm, ss, 554641))

	Executing SELECT on member_detail_types
	Column names: ['type', 'display_name']
	Number of records: 1
	First record: ('callsign', 'Amateur radio callsign')

	Executing SELECT on member_details
	Column names: ['member', 'type', 'entered', 'value']
	Number of records: 1
	First record: ('*****', 'callsign', datetime.datetime(yyyy, mm, dd, hh, mm, ss, 76232), 'none')

	Executing SELECT on member_language_skill
	Column names: ['member', 'language', 'skill', 'entered']
	Number of records: 3
	First record: ('*****', 'en', 'native', datetime.datetime(yyyy, mm, dd, hh, mm, ss, 914698))

	Executing SELECT on member_state
	Column names: ['ident', 'can_login', 'can_see', 'can_send', 'can_recv', 'blocked', 'hidden']
	Number of records: 9
	First record: ('nominated', False, False, False, False, False, False)

	Executing SELECT on member_vouch
	Column names: ['vouchor', 'vouchee', 'trustgroup', 'comment', 'entered', 'positive']
	Number of records: 19
	First record: ('*****', '*****', 'dims', '', datetime.datetime(yyyy, mm, dd, hh, mm, ss, 266320), True)

	Executing SELECT on message_catalog
	Column names: ['trustgroup', 'message_type', 'message_template']
	Number of records: 0

	Executing SELECT on message_types
	Column names: ['ident', 'descr']
	Number of records: 3
	First record: ('web_global_hello', 'Global public about Ops-t page')

	Executing SELECT on openid_associations
	Column names: ['uuid', 'assoc_type', 'session_type', 'mac_key', 'timestamp']
	Number of records: 0

	Executing SELECT on second_factor_types
	Column names: ['type', 'descr']
	Number of records: 3
	First record: ('TOTP', 'Time based One Time Password - TOPT')

..
