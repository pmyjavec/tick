chronograf
==========

[![Build Status](https://travis-ci.org/kbrebanov/ansible-chronograf.svg?branch=master)](https://travis-ci.org/kbrebanov/ansible-chronograf)

Installs and configures Chronograf.

Requirements
------------

This role requires Ansible 1.9 or higher.

Role Variables
--------------

| Name                                  | Default                       | Description                                                                          |
|:--------------------------------------|:------------------------------|:-------------------------------------------------------------------------------------|
| chronograf_version                    | 0.13                          | Version of Chronograf to install                                                     |
| chronograf_bind                       | "127.0.0.1:10000"             | TCP address that Chronograf should bind to                                           |
| chronograf_local_database             | /opt/chronograf/chronograf.db | Path to local database file to use or create for storing Chronograf application data |
| chronograf_query_response_bytes_limit | 2500000                       | Maximum response size in bytes, for queries that pass through Chronograf             |

Dependencies
------------

None

Example Playbook
----------------

Install Chronograf
```yaml
- hosts: all
  roles:
    - kbrebanov.chronograf
```

License
-------

BSD

Author Information
------------------

Kevin Brebanov
