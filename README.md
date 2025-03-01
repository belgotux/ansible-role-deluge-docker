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

## Docker configuration

### Mandatory
- `docker_deluge_traefik_domain` the domaine to match with Traefik labels for deluge

### Docker/traefik
Optionnal and can be inherit for other docker roles in the collection via variables file :
- `docker_deluge_traefik_network_name` your traefik network (default proxy-net)
- `docker_deluge_docker_user` your usual user to run a docker container (default the user running the playbook)
- `docker_deluge_docker_group` your usual group to run a docker container (default `docker`)

### Main docker configuration (optionnal)
- `docker_deluge_uid` define a system uid (default 202)
- `docker_deluge_gid` define a system group gid (default 202)
- `docker_deluge_user` user in the host for files's ownership (default dl)
- `docker_deluge_group` group (default dl)
- `docker_deluge_torrent_path` deluge data root path on the host (default /torrent) that is bind to /torrent inside the docker
- `docker_deluge_config` deluge config path on the host (default /etc/docker/deluge/config) that is bind to /config inside the docker
- `docker_deluge_sub_paths` list of paths used by deluge
  - `tmp` for current downloads
  - `complets` for finished downloads
  - `archives` to archive the .torrent files
- `docker_deluge_torrent_port` port to expose for torrent P2P (default 58946) that is bind to 58946 inside the docker
- `docker_deluge_dir` the directory for deluge (default `/etc/docker/deluge`)
- `docker_deluge_volumes_extra` list of extra volume lines for docker-compose file

### Second docker configuration (very optional)
- `docker_deluge_portmaping` web interface portmapping (default yes)
- `docker_deluge_port` port to use in portmaping mode (default 8112)
- `docker_deluge_api_portmaping` allow API portmapping, no needed if you using only webinterface (default no)
- `docker_deluge_api_port` port to use for API portmapping (default 58846)
- `docker_deluge_traefik` use Traefik labels as reverse proxy for https (default no)
- `docker_deluge_start` start the docker-compose directly at the end of configuration (default no).
- `docker_deluge_loglevel` set the loglevel output when running Deluge (default `error`) (image default is info for deluged and warning for delgued-web)
- `docker_deluge_service_name` container name (default `deluge`)

## Deluge configuration (optionnal)
When you see `-1 it's unlimited
### Core
Bandwidth :
- `docker_deluge_max_connections_global` Deluge max connexions (default `1200`)
- `docker_deluge_max_upload_slots_global` Deluge max upload slots (default `35`)
- `docker_deluge_max_download_speed` Deluge max donwload speed kbps (default `-1.0`)
- `docker_deluge_max_upload_speed` Deluge max upload speed kbps (default `-1.0`)
- `docker_deluge_max_half_open_connections` Deluge max half open connections (default `25`)
- `docker_deluge_max_connections_per_second` Deluge max connections per second (default `20`)

Bandwidth per torrent:
- `docker_deluge_max_connections_per_torrent` Deluge max connections per torrent kbps (default `-1`)
- `docker_deluge_max_download_speed_per_torrent` Deluge max download speed per torrent kbps (default `-1`)
- `docker_deluge_max_upload_slots_per_torrent` Deluge max upload slots per torrent kbps (default `-1`)
- `docker_deluge_max_upload_speed_per_torrent` Deluge max upload speed per torrent kbps (default `-1`)

Queue :
- `docker_deluge_max_active_downloading` Deluge max active download (default `10`)
- `docker_deluge_max_active_limit` Deluge max active torrent active (default `205`)
- `docker_deluge_max_active_seeding` Deluge max torrent seeding (default `200`)

### Plugin blocklist
- `docker_deluge_blocklist_url` URL to blocklists file (same for transmission) (default `https://raw.githubusercontent.com/Naunter/BT_BlockLists/master/bt_blocklists.gz`)

### Plugin execute
- `docker_deluge_execute_list` list of type (complete|added|removed) and script path. You need to use `docker_deluge_volumes_extra` to bidn script inside the container.

Dependencies
------------
Installed by the role :
- pip3
- pip lib for docker
  - `docker`

Example Playbook
----------------

## simple
```
- hosts: '{{ myhost | default('all') }}'
  remote_user: root

  roles:
    - role: belgotux.docker_deluge
```

## with traefik using docker collection

```
- name: Torrent machine
  hosts: vps
  remote_user: root

  roles:
    - role: belgotux.docker
      tags: docker
    - role: belgotux.docker_traefik
      tags: traefik
    - role: belgotux.docker_deluge
      tags: deluge
  variables:
    docker_deluge_service_name: 'deluge-test'
    docker_deluge_traefik: true
    docker_deluge_traefik_domain: deluge.yourdomain.tld

    docker_deluge_volumes_extra:
      - /usr/local/bin/wrapper_telegram.sh:/usr/local/bin/wrapper_telegram.sh:ro

    docker_deluge_execute_list:
      - ["complete", "/usr/local/bin/wrapper_telegram.sh"]

    docker_deluge_portmaping: false
    docker_deluge_max_download_speed: 30000.0
    docker_deluge_max_upload_speed: -1.0
    docker_deluge_max_active_downloading: 5
    docker_deluge_max_half_open_connections: 13
    docker_deluge_max_upload_slots_global: 18
    docker_deluge_start: true
```

License
-------

[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

Author Information
------------------

Belgotux
[MonLinux](https://www.monlinux.net)
