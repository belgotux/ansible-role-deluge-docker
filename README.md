Deluge-docker
=============

Role to create deluge user/group and directory to use in the cluster.
Need to use the same UID and GID on all the system's user.
Based on the image `linuxserver/deluge`.

Requirements
------------

- Docker and docker-compose installed (with [my role](https://galaxy.ansible.com/belgotux/docker) if needed [github](https://github.com/belgotux/ansible-role-docker))
- Ansible collection community.docker ([documentation](https://docs.ansible.com/ansible/latest/collections/community/docker/docker_compose_module.html)) : `ansible-galaxy collection install community.docker`

Role Variables
--------------
The role can work as it with the [default configuration](defaults/main.yml).

### Docker/traefik (can be inherit for other docker roles)
- `traefik_network_name` your traefik network (default proxy-net)
- `traefik_domain` Main domaine of Traefik, to create a subdomain deluge.yourdomain.tld
- `docker_user` your usual user to run a docker container (by default the first user with uid 1000)
- `docker_group` your usual group to run a docker container (by default the first user's group with gid 1000)
- `docker_conf` the docker configurations's repository (default /etc/docker)

### Main
- `deluge_uid` define a system uid (default 202)
- `deluge_gid` define a system group gid (default 202)
- `deluge_user` user in the host for files's ownership (default dl)
- `deluge_group` group (default dl)
- `deluge_torrent_path` deluge data root path on the host (default /torrent) that is bind to /torrent inside the docker
- `docker_deluge_config` deluge config path on the host (default /etc/docker/deluge/config) that is bind to /config inside the docker
- `deluge_sub_paths` list of paths used by deluge
  - `tmp` for current downloads
  - `complets` for finished downloads
  - `archives` to archive the .torrent files
- `docker_deluge_torrent_port` port to expose for torrent P2P (default 58946) that is bind to 58946 inside the docker

### Second
- `docker_deluge_portmaping` web interface portmapping (default yes)
- `docker_deluge_port` port to use in portmaping mode (default 8112)
- `docker_deluge_api_portmaping` allow API portmapping, no needed if you using only webinterface (default no)
- `docker_deluge_api_port` port to use for API portmapping (default 58846)
- `docker_deluge_traefik` use Traefik labels as reverse proxy for https (default no)
- `deluge_traefik_subdomain` the subdomaine to create with Traefik for deluge (default deluge)
- `docker_deluge_start` start the docker-compose directly at the end of configuration (default no).
- `docker_deluge_loglevel` set the loglevel output when running Deluge (default `error`) (image default is info for deluged and warning for delgued-web)
- `docker_deluge_service_name` container name (default `deluge`)



Dependencies
------------
Installed by the role :
- pip3
- pip lib for docker
  - `docker`

Example Playbook
----------------

```
- hosts: "{{myhost | default('all')}}"
  remote_user: root

  roles:
    - role: belgotux.docker_deluge
```

License
-------

[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

Author Information
------------------

Belgotux
[MonLinux](https://www.monlinux.net)
