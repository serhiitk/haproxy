
# HAProxy
Install HAProxy on host and configure backends ( *red/green/blue* ) to redirect HTTP requests by URL "path" prefix value - `/red`, `/green`, `/blue` .
Backends runs in docker containers on remote Docker-server by Docker API (use repository: **docker_systemd**).

## Variables
Variables defines in `./vars/default_vars.yml`

    docker_api_url: "tcp://<remote_docker_ip>:2375"
    ....
    container_name_prefix: "backend_"
    haproxy_backend_list:
      - name: 'red'
        host: '<remote_docker_ip>'
        port: 8085
      - name: 'green'
        host: '<remote_docker_ip>'
        port: 8086
      - name: 'blue'
        host: '<remote_docker_ip>'
        port: 8087    
    ....
    and etc.

## Prerequisites
### Preparation Docker server (if it's the first start)
- start deployment Docker server (from repository: **docker_systemd**): 

      $ ansible-playbook deploy_containers.yml

## Deploy HAProxy Server
- start deployment HAProxy with configure backends ( *red/green/blue* ) : 

      $ ansible-playbook deploy_haproxy.yml

### HAProxy configuration file
Configuration template file located at `./templates/haproxy.cfg.j2` defines:

+ authentication userlist (default user and encrypted password: `user1`)
+ show HAProxy Statistics Report page on /stats URL on port 5001
+ `http-in` frontend which proxies to the backends
+ list of static backends for servers - *red/green/blue*
+ round robin balancing between *red/green/blue* backends  


## Remove backend containers ( *red/green/blue* ) from Remote Docker host

    $ ansible-playbook remove_backend_nodes.yml

***NOTE:*** HAProxy Server must be up and running before. 
