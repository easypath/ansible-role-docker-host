Docker Host
===========
- Installs Docker Community Edition (CE) on CentOS 7.4
- Pre-configured with [user namespaces](https://docs.docker.com/engine/security/userns-remap/) for increased security. **NOTE:** reboot is required to activate kernel changes.
  - After user namespaces are enabled, host bind mounts will no longer work
  - Containers will be unable to bind to Docker engine on local UNIX socket; specifically affects managment tools like [Portainer](https://portainer.io/); workaround is to configure Docker engine with TLS
- Generates TLS certificates and secures Docker engine (requires Galaxy role [easypath.generate-tls-certs](https://galaxy.ansible.com/easypath/generate-tls-certs/))
  - Configures Docker client to connect using TLS by default, otherwise need to specify TLS cert and key everytime running `docker` command; configured per profile and requires client certificate, see [here](https://docs.docker.com/engine/security/https/#secure-by-default) for more info)
- Docker engine binds new containers to localhost by default if no IP is specified
- Configures Docker Swarm-related firewalld rules

**WARNING: re-running certificate generation in the same output folder will overwrite any existing certs and keys!**
**Note:** after configuring TLS, no longer need to prefix `docker` commands with `sudo`


Role Variables
--------------
- See `defaults/main.yml`


Example Playbook
----------------
```
- hosts: all
  vars_prompt:
    - name: "generate_tls_certs"
      prompt: "> Generate TLS certificates and keys? WARNING: re-running this will overwrite any existing certs and keys!"
      private: no
      default: false

    - name: "config_tls"
      prompt: "> Configure Docker engine for TLS?"
      private: no
      default: false

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

