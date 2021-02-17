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

=== WE ARE HERE ===

