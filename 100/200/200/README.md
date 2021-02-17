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

Now we create the basic Traefik configuration file:

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

NOTE: Adjust the ```email``` to your email address.

=== WE ARE HERE ===

