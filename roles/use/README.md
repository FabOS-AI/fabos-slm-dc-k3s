Ansible Role: fabos.k3s.use
=========

The role provides task files for deploying and undeploying workloads defined by docker-compose files in k3s clusters.

Requirements
------------

The role is only tested for specific Linux Distributions/Versions (see meta/main.yml).

Role Variables
--------------

- **deploy**: requires a unique service id (e.g. UUID) and the docker-compose definition of the workload
- **undeploy**: requires the unique service id used for deployment

Dependencies
------------

None.

Example Playbook
----------------

See playbook files in root folder:

- deploy.yml
- undeploy.yml

License
-------

MIT

Author Information
------------------

Benjamin Goetz - Fraunhofer IPA
