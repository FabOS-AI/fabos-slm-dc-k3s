Ansible Role: fabos.k3s.setup
=========

The role provides task files for installing and uninstalling k3s on target hosts as well as scaling the number of k3s agents.

Requirements
------------

The role is only tested for specific Linux Distributions/Versions (see meta/main.yml).

Role Variables
--------------

No parameters required.

Dependencies
------------

None.

Example Playbook
----------------

See playbook files in root folder:

- install.yml
- uninstall.yml
- scaleup.yml
- scaledown.yml

License
-------

MIT

Author Information
------------------

Benjamin Goetz - Fraunhofer IPA
