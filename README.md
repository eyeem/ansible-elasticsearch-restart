
=========

An Ansible role for performing safe Elasticsearch rolling restarts, without the cluster ever getting red.


Requirements
------------

The task is using the `shell` module to perform `curl` requests to the local elasticsearch instance.

Role Variables
--------------

````yaml
es_http_port: 9200
es_service_name: elasticsearch
````

Dependencies
------------

The `elasticsearch-restart` role does not require any external dependencies.

Example Playbook
----------------

This role can be used within a playbook that also takes care of pre- and post- tasks that might also be needed to safely restart your cluster nodes.

````yaml
---

- hosts : "elasticsearch_cluster"
  serial: 1
  pre_tasks:
    - include: includes/monitoring_deregister.yml
    - include: includes/balancer_deregister.yml
  roles:
    - elasticsearch-restart
  post_tasks:
    - include: includes/balancer_register.yml
    - include: includes/monitoring_register.yml
````

License
-------

BSD

Contributors
------------------
* Michele Palmia (`michele`)
* Joé Schaul (`joe`)

Please email us `@eyeem.com`
