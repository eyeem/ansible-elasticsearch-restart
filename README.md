An Ansible role for performing safe [Elasticsearch rolling restarts](https://www.elastic.co/guide/en/elasticsearch/guide/current/_rolling_restarts.html), without the cluster ever getting red.

As specified on the documentation page linked above, the current role performs the following steps:
* disable shard reallocation,
* restart the Elasticsearch service,
* wait for the machine to rejoin the cluster (checking number of nodes),
* reenable shard reallocation,
* wait for the cluster to return to green state.

Requirements
------------

The role uses the `shell` module to perform `curl` requests to the local elasticsearch instance. You should ensure `shell` is installed on the host machines before invoking this role.

Role Variables
--------------

````yaml
es_http_port: 9200
es_listening_host: localhost
es_service_name: elasticsearch
````

All commands will be executed locally on the host machine, calling `curl` on `es_listening_host:es_http_port`. The name of the elasticsearch service can be customized using the `es_service_name` variable.

Dependencies
------------

The `elasticsearch-restart` role does not have any external dependencies.

Example Playbook
----------------

````yaml
---
- hosts : "elasticsearch_cluster"
  serial: 1
  roles:
    - elasticsearch-restart
````

While you can simply use the `elasticsearch-restart` role like shown in the snippet above, you likely want to perform some other operations on each node before actually starting the controlled restart. To do so, you can use this role within a playbook that also takes care of pre- and post- tasks like registering and deregistering the node from monitoring and from any load balancers you might be using. Please refer to the [Ansible documentation](http://docs.ansible.com/ansible/guide_rolling_upgrade.html) for further details.

````yaml
---

- hosts : "elasticsearch_cluster"
  serial: 1
  pre_tasks:
    - include: monitoring_deregister.yml
    - include: balancer_deregister.yml
  roles:
    - elasticsearch-restart
  post_tasks:
    - include: balancer_register.yml
    - include: monitoring_register.yml
````

License
-------

Apache v2

Contributors
------------------
* Michele Palmia (`michele`)
* Joé Schaul (`joe`)

Please email us `@eyeem.com`
