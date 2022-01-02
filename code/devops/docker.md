# Docker

## Installation

Linux: 

```bash
$ sudo apt-get update
$ docker install # if not installed - returns script to install
# $ sudo apt install docker.io

```

## Notes

The container only lives as long as the process inside it is alive.
If the server inside the container stops or crashes, it exits the container.

## Commands

### General

```bash
> docker run <image/container> # run image if exists, otherwise pull from docker hub
> # docker run nginx
> docker run -d <container> # runs container in background (detached)
> docker attach <container id> # attaches container that was detached

> docker ps     # list all running containers
> docker ps -a  # list all containers

> docker stop <container> # container id or container name
> docker rm <container> # container id or container name

> docker images # list all images
> docker rmi <image> # make sure no containers are running off that image first

> docker pull <image>
```

### Execs 

You can also run execs on a container. For example:

```bash
> docker exec <container> cat /etc/hosts
```


### Advanced Run

```bash
> docker run redis      # no tag means use default version 
> docker run redis:4.0  # apply tag to tell docker redis version
```