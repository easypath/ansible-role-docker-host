Docker Host
===========
- Installs Docker Community Edition (CE) on CentOS 7.4
- Pre-configured with [user namespaces](https://docs.docker.com/engine/security/userns-remap/) for increased security. **NOTE:** reboot is required to activate kernel changes.
  - After user namespaces are enabled, host bind mounts will no longer work
  - Containers will be unable to bind to Docker engine on local UNIX socket; specifically affects managment tools like [Portainer](https://portainer.io/); workaround is to configure Docker engine with TLS
- Generates TLS certificates and secures Docker engine
  - Configures Docker client to connect using TLS by default, otherwise need to specify TLS cert and key everytime running `docker` command; configured per profile and requires client certificate, see [here](https://docs.docker.com/engine/security/https/#secure-by-default) for more info)
- Docker engine binds new containers to localhost by default if no IP is specified
- Configures Docker Swarm-related firewalld rules

**Note: after configuring TLS, no longer need to prefix `docker` commands with `sudo`**


Requirements
------------
None.


Role Variables
--------------
- config_user_namespaces
- config_tls
- tls_ca_cert
- tls_ca_key
- config_profile_docker_client_tls
- profile_user
- tls_client_cert
- tls_client_key
- cert_src_path
- cert_dest_path
- cert_key_size
- cert_valid_days
- config_swarm_fw
- fw_zone_name
- docker_engine_bind_ip
- docker_ce_version
- docker_repo_baseurl
- repo_gpgkey


Dependencies
------------
None.


Example Playbook
----------------

```
- hosts: all
  vars_prompt:
    - name: "config_tls"
      prompt: "> Generate certificate and configure Docker engine for TLS"
      private: no
      default: true

    - name: "config_swarm_fw"
      prompt: "> Configure firewall rules for Docker Swarm?"
      private: no
      default: true

    - name: "reboot_server"
      prompt: "> Reboot server after setup? Required to activate kernel changes."
      private: no
      default: false

  tasks:
    - name: Install Docker
      import_role: 
        name: docker-host

    - name: Reboot server (required to activate kernel changes)
      command: reboot
      become: yes
      when: reboot_server|bool

```


License
-------
BSD


Author Information
------------------
[EasyPath IT Solutions Inc.](https://www.easypath.ca)

