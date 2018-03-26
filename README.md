YasuhiroABE.test-metaldap
==========================

An example of back_meta openldap module configuration for the YasuhiroABE.test-transldap galaxy role.

This module configures the server3 openldap servers as like as follows:

[server1]

* Top-level (Example) directory (DN: dc=example,dc=com)
    * ou=people,dc=example,dc=com
    * ou=group,dc=example,dc=com 

[server2] YasuhiroABE.test-transldap

* Translucent directory against server1 (override some attributes on ou=people,dc=example,dc=com of serve1)

[server3] YasuhiroABE.test-metaldap

* Unified directory: ou=proxy,dc=example,dc=com
    * ou=people,ou=proxy,dc=example,dc=com
    * ou=group,ou=proxy,dc=example,dc=com

Requirements
------------

This role is tested on the following platforms.

### Ansible
- Version 2.4

### Distributions
- Ubuntu 16.04

Role Variables
--------------

### Default
    metaldap_init: true
    metaldap_removal_filepath:
      - '/var/lib/ldap/data.mdb'
      - '/var/lib/ldap/lock.mdb'
      - "{{ metaldap_basedn_dbdirectory }}"
      
    metaldap_add_dirinfo:
      - { path: "{{ metaldap_basedn_dbdirectory }}", owner: 'openldap', group: 'openldap', mode: '0750' }

    metaldap_additional_schema:
      - ppolicy

    metaldap_ldapadd_cn_config_files:
      - metaldap_01_add_modules.ldif
      - metaldap_02_create_metadb.ldif

    metaldap_ldapadd_backenddb_files: []

    metaldap_copy_ldif_files:
      - metaldap_01_add_modules.ldif
      - metaldap_02_create_metadb.ldif

    metaldap_copy_ldif_perms:
      owner: root
      group: root
      mode: '0600'
      
    metaldap_basedn_suffix: "dc=example,dc=com"
    metaldap_basedn_prefix: "ou=proxy"
    metaldap_cn_admin_dnprefix: "cn=admin"
    metaldap_cn_admin_password: "transpasswd"
    metaldap_basedn_dbdirectory: "/var/lib/ldap/metaproxy"

    ## using in metaldap_02_create_metadb.ldif
    metaldap_metadb_index: 1
    metaldap_metatarget_items:
      - { index: 0, suffix: "ou=people", dburi: "ldap://transldap.example.com:389/ }}" }
      - { index: 1, suffix: "ou=group", dburi: "ldap://transldap.example.com:389/ }}" }


Dependencies
------------

* This role removes existing directory files specified with the transldap_removal_filepath variable.

Example Playbook
----------------

Following vars entries need to be configured for your environment.

    - hosts: all
      vars:
        metaldap_basedn_suffix: "dc=example,dc=org"
        metaldap_basedn_prefix: "ou=proxy"
        metaldap_metatarget_items:
          - { index: 0, suffix: "ou=People", dburi: "ldap://transldap.example.com:389" }
          - { index: 1, suffix: "ou=Group", dburi: "ldap://transldap.example.com:389" }
      roles:
         - YasuhiroABE.test-metaldap

License
-------

Apache License 2.0

Author Information
------------------

[Yasuhiro ABE](http://www.yasundial.org/foaf.xml)

