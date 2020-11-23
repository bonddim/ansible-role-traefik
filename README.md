# Ansible Role: traefik

[![Ansible Role](https://img.shields.io/ansible/role/51648?label=galaxy&logo=ansible)](https://galaxy.ansible.com/bonddim/traefik)
[![Ansible Role Downloads](https://img.shields.io/ansible/role/d/51648?logo=ansible)](https://galaxy.ansible.com/bonddim/traefik)
[![Ansible Quality Score](https://img.shields.io/ansible/quality/51648?logo=ansible)](https://galaxy.ansible.com/bonddim/traefik)
[![Workflow](https://img.shields.io/github/workflow/status/bonddim/ansible-role-traefik/Molecule?logo=github)](https://github.com/bonddim/ansible-role-traefik/actions)
[![License](https://img.shields.io/github/license/bonddim/ansible-role-traefik)](https://github.com/bonddim/ansible-role-traefik/blob/main/LICENSE)


Deploy and configure [Traefik](https://doc.traefik.io/traefik/) docker container

Clean role execution will give you:
  - running traefik instance on 80 port
  - enabled traefik dashboard secured with basic authentication
  - security options for https

## Requirements
Docker daemon and Docker SDK for Python is required on target host

## Dependencies
Minimal playbook to install Docker daemon and SDK on Debian family OS
```sh
ansible-galaxy role install bonddim.docker
```
```yaml
---
- hosts: Debian,Ubuntu
  gather_facts: true
  roles:
    - bonddim.docker
  tasks:
    - name: Install docker python library
      package:
        name: python3-docker
        state: present
```
Install Docker SDK from pip
```sh
ansible-galaxy role install bonddim.docker geerlingguy.pip
```
```yaml
---
- hosts: all
  gather_facts: true
  roles:
    - bonddim.docker
    - role: geerlingguy.pip
      pip_install_packages: docker
```

## Role Variables
Variables with default values from _defaults/main.yml_
```yaml
traefik_data: /opt/traefik  # path on host to store traefik data
traefik_docker_network: bridge  # docker network name for traefik proxy
traefik_host_domainname: ""  # your domain name
traefik_env: {}  # dict with environment variables, mostly used for acme dns provider settings
traefik_dashboard_enable: true  # enable/disable dashboard
traefik_dashboard_middlewares: auth-basic@file  # apply middlewares for dashboard
traefik_basic_auth_user: admin  # user name for basic auth
traefik_basic_auth_pass: $apr1$MR6nE9XC$lC2fnUmGLmsZDGqNlcdxU0  # password for basic auth generated with htpasswd (admin)
traefik_log_level: ERROR  # traefik log level
traefik_log_format: json  # traefik log format
traefik_enable_https: false  # enable/disable https entrypoint
traefik_enable_https_redirect: true  # create permanent redirect from http to https
traefik_tls_config: modern  # tls settings https://ssl-config.mozilla.org
traefik_tls_resolver: acme  # tls resolve method, possible values: acme, user
traefik_tls_certificates: []  # list of user certificates when traefik_tls_resolver: user
  # - cert: /path/to/cert
  #   key: /path/to/key
  # - cert: /path/to/another/cert
  #   key: /path/to/another/key
# ACME (Let's Encrypt) https://doc.traefik.io/traefik/https/acme
traefik_acme_email: ""  # email for acme registration
traefik_acme_challenge: dns  # possible values: dns, tls, http
traefik_acme_dns_provider: ""  # your dns provider
traefik_acme_staging: false  # enable staging server for debug
traefik_provider_docker: true  # enable/disable docker provider
traefik_provider_docker_exposebydefault: false  # enable/disable expose by default
traefik_provider_docker_defaultrule: ""  # default rule for docker provider
traefik_provider_docker_endpoint: ""  # default docker endpoint unix:///var/run/docker.sock
traefik_http_routes: []  # user-defined http routes to other servers/services on host/network
  # - name: ""  # required
  #   rule: ""  # optional. Default is 'Host(`route_name.traefik_host_domainname`)' or 'Host(`route_name`)'
  #   service: ""  # optional. Default is route_name
  #   url: []  # required. List of server(s) url, example: [ http://private-ip-server-1/ ]
  #   middlewares: []  # optional. List of middlewares for route
  #   priority: ""  # optional. Default is '0'
```

## Example Playbook
```yaml
- name: minimal setup with http
  hosts: all
  roles:
    - bonddim.traefik

- name: minimal setup with https
  hosts: all
  vars:
    traefik_enable_https: true
    traefik_acme_email: email@example.com
    traefik_host_domainname: example.com
    traefik_acme_dns_provider: duckdns
    traefik_env:
      DUCKDNS_TOKEN: "duckdns_token_value"
  roles:
    - bonddim.traefik
```

## License
MIT

## Author Information
[Dmytro Bondar](https://github.com/bonddim)
