# Ansible role: labocbz.install_filebeat

![Licence Status](https://img.shields.io/badge/licence-MIT-brightgreen)
![CI Status](https://img.shields.io/badge/CI-success-brightgreen)
![Testing Method](https://img.shields.io/badge/Testing%20Method-Ansible%20Molecule-blueviolet)
![Testing Driver](https://img.shields.io/badge/Testing%20Driver-docker-blueviolet)
![Language Status](https://img.shields.io/badge/language-Ansible-red)
![Compagny](https://img.shields.io/badge/Compagny-Labo--CBZ-blue)
![Author](https://img.shields.io/badge/Author-Lord%20Robin%20Crombez-blue)

## Description

![Tag: Ansible](https://img.shields.io/badge/Tech-Ansible-orange)
![Tag: Debian](https://img.shields.io/badge/Tech-Debian-orange)
![Tag: Filebeat](https://img.shields.io/badge/Tech-Filebeat-orange)
![Tag: SSL/TLS](https://img.shields.io/badge/Tech-SSL%2FTLS-orange)

An Ansible role install and configure Filebeat in your server

The Filebeat installation role automates the deployment and configuration of Filebeat, a lightweight log shipper. This role streamlines the setup process, allowing you to easily manage Filebeat's settings. You can specify a path to log configurations, making it convenient to add and manage various log sources. With options to configure worker count and reload intervals, Filebeat efficiently processes log data. Additionally, you can direct log data to Logstash using plain HTTP or HTTPS, with options for SSL and mTLS encryption.

The role also offers log retention configuration for managing log storage duration (Filebeat's logs, not apps logs). Overall, the role simplifies Filebeat deployment, enabling seamless integration with Logstash or other outputs while ensuring secure log transmission and scalability.

Any logs harvester configuration have to be deployed after this installation and are not provided byt this role.

## Folder structure

By default Ansible will look in each directory within a role for a main.yml file for relevant content (also man.yml and main):

```PYTHON
.
├── README.md  # Contains an overview of the role and its purpose.
├── defaults
│   ├── main.yml  # Contains default variables for the role that can be overridden by users.
│   └── README.md  # Contains documentation for the default variables.
├── files
│   └── README.md  # Contains documentation for the files in the directory.
├── handlers
│   ├── main.yml  # Contains handlers that can be called by tasks within the role.
│   └── README.md  # Contains documentation for the handlers.
├── meta
│   ├── main.yml  # Contains metadata about the role, including dependencies and supported platforms.
│   └── README.md  # Contains documentation for the role metadata.
├── tasks
│   ├── main.yml  # Contains tasks to be executed by the role on the managed nodes.
│   └── README.md  # Contains documentation for the tasks.
├── templates
│   └── README.md  # Contains documentation for the templates.
└── vars
    ├── main.yml  # Contains variables that are specific to the role and are not meant to be overridden.
    └── README.md  # Contains documentation for the role variables.
```

## Tests and simulations

### Basics

You have to run multiples tests. *tests with an # are mandatory*

```MARKDOWN
# lint
# syntax
# converge
# idempotence
# verify
side_effect
```

Executing theses test in this order is called a "scenario" and Molecule can handle them.

Molecule use Ansible and pre configured playbook to create containers, prepare them, converge (run the role) and verify its execution.
You can manage multiples scenario with multiples tests in order to get a 100% code coverage.

This role contains a ./tests folder. In this folder you can use the inventory or the tower folder to create a simualtion of a real inventory and a real AWX / Tower job execution.

### Command reminder

```SHELL
# Check your YAML syntax
yamllint -c ./.yamllint .

# Check your Ansible syntax and code security
ansible-lint --config=./.ansible-lint .

# Execute and test your role
molecule lint
molecule create
molecule list
molecule converge
molecule verify
molecule destroy

# Execute all previous task in one single command
molecule test
```

## Installation

To install this role, just copy/import this role or raw file into your fresh playbook repository or call it with the "include_role/import_role" module.

## Usage

### Vars

Some vars a required to run this role:

```YAML
---
install_filebeat_major_version: "8"

install_filebeat_config_path: "/etc/filebeat"
install_filebeat_confs_path: "{{ install_filebeat_config_path }}/conf.d"

install_filebeat_ssl_authorities: "/etc/ssl/cacert"
install_filebeat_ssl_path: "{{ install_filebeat_config_path }}/ssl"
install_filebeat_ssl_crt: "{{ install_filebeat_config_path }}/cert.crt"
install_filebeat_ssl_key: "{{ install_filebeat_config_path }}/key.key"

install_filebeat_workers: 5
install_filebeat_confs_reload: 10s
install_filebeat_loglevel: "info"
install_filebeat_log_keeping: 31

install_filebeat_logstash_client_auth: false
install_filebeat_logstash_ssl: false
install_filebeat_logstash_hosts:
  - "127.0.0.1:9200"

install_filebeat_group: "filebeat"

```

The best way is to modify these vars by copy the ./default/main.yml file into the ./vars and edit with your personnals requirements.

You can set vars in the template model in Ansible AWX / Tower or just surchage them during the playbook call.

In order to surchage vars, you have multiples possibilities but for mains cases you have to put vars in your inventory and/or on your AWX / Tower interface.

```YAML
# From inventory
---
inv_prepare_host_users:
  - login: "filebeat"
    group: "filebeat"

inv_install_filebeat_major_version: "8"

inv_install_filebeat_config_path: "/etc/filebeat"
inv_install_filebeat_confs_path: "{{ inv_install_filebeat_config_path }}/conf.d"

inv_install_filebeat_ssl_authorities: "{{ inv_install_filebeat_ssl_path }}/My-Local-CA-Authority/My-Local-CA-Authority.crt"
inv_install_filebeat_ssl_path: "{{ inv_install_filebeat_config_path }}/ssl"
inv_install_filebeat_ssl_crt: "{{ inv_install_filebeat_ssl_path }}/cert.crt"
inv_install_filebeat_ssl_key: "{{ inv_install_filebeat_ssl_path }}/key.key"

inv_install_filebeat_logstash_ssl: true
inv_install_filebeat_logstash_hosts:
  - "127.0.0.1:5054"
inv_install_filebeat_logstash_client_auth: true
inv_install_filebeat_confs_reload: "10s"
inv_install_filebeat_loglevel: "debug"
inv_install_filebeat_log_keeping: 31
inv_install_filebeat_workers: 5

inv_install_filebeat_group: "filebeat"

```

```YAML
# From AWX / Tower
---

```

### Run

To run this role, you can copy the molecule/default/converge.yml playbook and add it into your playbook:

```YAML
- name: "Include labocbz.install_filebeat"
    tags:
    - "labocbz.install_filebeat"
    vars:
    install_filebeat_major_version: "{{ inv_install_filebeat_major_version }}"
    install_filebeat_config_path: "{{ inv_install_filebeat_config_path }}"
    install_filebeat_cluster_name: "{{ inv_install_filebeat_cluster_name }}"
    install_filebeat_confs_path: "{{ inv_install_filebeat_confs_path }}"
    install_filebeat_ssl_authorities: "{{ inv_install_filebeat_ssl_authorities }}"
    install_filebeat_ssl_path: "{{ inv_install_filebeat_ssl_path }}"
    install_filebeat_logstash_ssl: "{{ inv_install_filebeat_logstash_ssl }}"
    install_filebeat_logstash_hosts: "{{ inv_install_filebeat_logstash_hosts }}"
    install_filebeat_logstash_client_auth: "{{ inv_install_filebeat_logstash_client_auth }}"
    install_filebeat_group: "{{ inv_install_filebeat_group }}"
    logstash_workers: "{{ inv_logstash_workers }}"
    ansible.builtin.include_role:
    name: "labocbz.install_filebeat"
```

## Architectural Decisions Records

Here you can put your change to keep a trace of your work and decisions.

### 2023-06-02: First Init

* First init of this role with the bootstrap_role playbook by Lord Robin Crombez

## Authors

* Lord Robin Crombez

## Sources

* [Ansible role documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
* [Ansible Molecule documentation](https://molecule.readthedocs.io/)
