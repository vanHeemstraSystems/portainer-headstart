# 300 Portainer

## 100 Setup and configure Portainer

Let’s get started by setting up Portainer.

First, create a directory for our container:

```
$ sudo mkdir -p /portainer-headstart/containers/portainer
```

If not yet created, create a Docker Compose file for Portainer as /portainer-headstart/containers/portainer/docker-compose.yml:

```
version: '3'

services:
  portainer:
    image: portainer/portainer:latest
    container_name: portainer
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./data:/data
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.portainer.entrypoints=http"
      - "traefik.http.routers.portainer.rule=Host(`portainer.example.com`)"
      - "traefik.http.middlewares.portainer-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.portainer.middlewares=portainer-https-redirect"
      - "traefik.http.routers.portainer-secure.entrypoints=https"
      - "traefik.http.routers.portainer-secure.rule=Host(`portainer.example.com`)"
      - "traefik.http.routers.portainer-secure.tls=true"
      - "traefik.http.routers.portainer-secure.tls.certresolver=http"
      - "traefik.http.routers.portainer-secure.service=portainer"
      - "traefik.http.services.portainer.loadbalancer.server.port=9000"
      - "traefik.docker.network=proxy"

networks:
  proxy:
    external: true
```

Please change the host rule at "traefik.http.routers.portainer.rule=Host(`portainer.example.com`)" and "traefik.http.routers.portainer-secure.rule=Host(`portainer.example.com`)" to your subdomain. Here: ***portainer.vanheemstrasystems.com***

=== WE ARE HERE ===
