# 200 Traefik

## 100 Setup and configure Traefik with Let’s Encrypt

Let’s get started by setting up Traefik.

First, create a directory for our container:

```
$ sudo mkdir -p /opt/containers/traefik
```

Create the data folder and config files for Traefik:

```
$ sudo mkdir -p /opt/containers/traefik/data
touch /opt/containers/traefik/data/acme.json
chmod 600 /opt/containers/traefik/data/acme.json
touch /opt/containers/traefik/data/traefik.yml
```

The ```acme.json``` file is the storage file for the HTTPS certificates.

Now we create the basic Traefik configuration file (/opt/containers/traefik/data/traefik.yml):

```
api:
  dashboard: true

entryPoints:
  http:
    address: ":80"
  https:
    address: ":443"

providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false

certificatesResolvers:
  http:
    acme:
      email: willem@vanheemstrasystems.com
      storage: acme.json
      httpChallenge:
        entryPoint: http
```

traefik.yml

NOTE: Please change the email address for the certificatesresolvers.

If not yet created, create a Docker Compose file for Traefik as k8s/traefik/docker-compose.yml:

```
version: '3'

services:
  traefik:
    image: traefik:v2.0
    container_name: traefik
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    ports:
      - 80:80
      - 443:443
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./data/traefik.yml:/traefik.yml:ro
      - ./data/acme.json:/acme.json
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.traefik.entrypoints=http"
      - "traefik.http.routers.traefik.rule=Host(`traefik.example.com`)"
      - "traefik.http.middlewares.traefik-auth.basicauth.users=USER:PASSWORD"
      - "traefik.http.middlewares.traefik-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.traefik.middlewares=traefik-https-redirect"
      - "traefik.http.routers.traefik-secure.entrypoints=https"
      - "traefik.http.routers.traefik-secure.rule=Host(`traefik.example.com`)"
      - "traefik.http.routers.traefik-secure.middlewares=traefik-auth"
      - "traefik.http.routers.traefik-secure.tls=true"
      - "traefik.http.routers.traefik-secure.tls.certresolver=http"
      - "traefik.http.routers.traefik-secure.service=api@internal"

networks:
  proxy:
    external: true
```
k8s/traefik/docker-compose.yml

Using this compose file, Traefik will also expose a dashboard (```"traefik.http.routers.traefik-secure.service=api@internal"```). 

Please change the host rule at ```"traefik.http.routers.traefik.rule=Host(`traefik.example.com`)"``` and ```"traefik.http.routers.traefik-secure.rule=Host(`traefik.example.com`)"``` to your subdomain. Here: ***traefik.vanheemstrasystems.com***

In addition, change the credentials at ```"traefik.http.middlewares.traefik-auth.basicauth.users=USER:PASSWORD"```. Those credentials must be in htpasswd format.

To generate htpasswd credentials, you can use the following command. 

Change <USER> and <PASSWORD.
                             
```
echo $(htpasswd -nb <USER> <PASSWORD>) | sed -e s/\\$/\\$\\$/g                             
```

Once that’s done we can create the proxy network an fire up Traefik:

```
$ docker network create proxy
cd /opt/containers/traefik
$ docker-compose up -d
```

Visit traefik.vanheemstrasystems.com and enjoy the new dashboard.
