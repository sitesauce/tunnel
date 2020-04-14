# Sitesauce Tunnel

[![Build Status](https://travis-ci.org/sitesauce/tunnel.svg?branch=master)](https://travis-ci.org/sitesauce/tunnel)

A mantained fork of the [localtunel server](https://github.com/localtunnel/server) used to temporally expose local sites when deploying via the [Sitesauce CLI](https://github.com/sitesauce/cli)

## Overview

We have configured our own server for the CLI to connect to, but uou can, easily set up and run your own server and connect using the [Localtunnel CLI](https://github.com/localtunnel/localtunnel). In order to run your own server you must ensure that your server can meet the following requirements:

* You can set up DNS entries for your `domain.tld` and `*.domain.tld` (or `sub.domain.tld` and `*.sub.domain.tld`).
* The server can accept incoming TCP connections for any non-root TCP port (i.e. ports over 1000).

The above are important as the client will ask the server for a subdomain under a particular domain. The server will listen on any OS-assigned TCP port for client connections.

#### setup

```shell
# pick a place where the files will live
git clone git://github.com/sitesauce/tunnel.git
cd tunnel
yarn

# server set to run on port 1234
yarn run start --port 1234
```

The server is now running and waiting for client requests on port 1234. You will most likely want to set up a reverse proxy to listen on port 80 (or start the tunnel on port 80 directly).

**NOTE** By default, this script will use subdomains for clients, if you plan to host your server itself on a subdomain you will need to use the _--domain_ option and specify the domain name behind which you are hosting localtunnel. (i.e. my-localtunnel-server.sitesauce.app)

#### Connect to your server

You can use the [Localtunnel CLI](https://github.com/localtunnel/localtunnel) with the `--host` flag to connect to your server.

```shell
lt --host http://sub.example.tld:1234 --port 9000
```

You will be assigned a URL similar to `heavy-puma-9.sub.example.com:1234`.

If your server is acting as a reverse proxy (i.e. nginx) and is able to listen on port 80, then you do not need the `:1234` part of the hostname for the `lt` client.

## REST API

### POST /api/tunnels

Create a new tunnel. A client can post to this enpoint to request a new tunnel with a specific name or a randomly assigned name.

### GET /api/status

General server information.

## Deploy

You can deploy your own server using the prebuilt docker image.

**Note** This assumes that you have a proxy in front of the server to handle the http(s) requests and forward them to the server on port 3000. You can use the [localtunnel-nginx](https://github.com/localtunnel/nginx) repo to accomplish this.

If you do not want ssl support for your own tunnel (not recommended), then you can just run the below with `--port 80` instead.

```
docker run -d \
    --restart always \
    --name sitesauce-tunnel \
    --net host \
    sitesauce/tunnel:latest --port 3000
```
