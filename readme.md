[![GitHub stars](https://img.shields.io/github/stars/taichunmin/docker-serveo.svg)](https://github.com/taichunmin/docker-serveo/stargazers)
[![GitHub license](https://img.shields.io/github/license/taichunmin/docker-serveo.svg)](https://github.com/taichunmin/docker-serveo/blob/master/LICENSE)
![Docker Stars](https://img.shields.io/docker/stars/taichunmin/serveo.svg)
![Docker Pulls](https://img.shields.io/docker/pulls/taichunmin/serveo.svg)
![Docker Automated build](https://img.shields.io/docker/automated/taichunmin/serveo.svg)
![Docker Build Status](https://img.shields.io/docker/build/taichunmin/serveo.svg)

# docker for serveo.net

[https://serveo.net](https://serveo.net) is an alternative for ngrok. [taichunmin/serveo](https://hub.docker.com/r/taichunmin/serveo) can let you secure URL to your localhost server through any NAT or firewall in Docker. And [taichunmin/serveo-server](https://hub.docker.com/r/taichunmin/serveo-server) can let you host your own serveo.

## Usage

1. write a `docker-compose.yml` file.

```yml
version: '2'

services:
  serveo:
    image: taichunmin/serveo:latest
    tty: true
    stdin_open: true
    # see https://serveo.net/ for more options
    command: autossh -M 0 -o ServerAliveInterval=60 -o ServerAliveCountMax=3 -o ExitOnForwardFailure=yes -o StrictHostKeyChecking=no -R 80:nginx:80 serveo.net
  nginx:
    image: nginx:latest
```

2. use `docker-compose up -d` to start container.

3. you need to use `docker-compose logs serveo` to see your new URL.

## Demo

```bash
$ git clone https://github.com/taichunmin/docker-serveo.git

$ sudo docker-compose up -d

$ sudo docker-compose logs serveo

Attaching to dockerserveo_serveo_1
serveo_1  | Warning: Permanently added 'serveo.net,195.201.91.242' (RSA) to the list of known hosts.
serveo_1  | Forwarding HTTP traffic from https://proinde.serveo.net
serveo_1  | Press g to start a GUI session and ctrl-c to quit.
serveo_1  | HTTP request from 125.227.205.43 to https://proinde.serveo.net/
serveo_1  | HTTP request from 125.227.205.43 to https://proinde.serveo.net/favicon.ico
```

## LICENSE

MIT License
