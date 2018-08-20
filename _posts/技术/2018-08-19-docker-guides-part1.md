---
layout: post
title: docker概念介绍及安装
author: sincat
date: 2018-08-19 12:38:15 +0800
category: 技术
tags: docker install

published: true
---

## Docker概念

Docker is a platform for developers and sysadmins to develop, deploy, and run applications with containers. The use of Linux containers to deploy applications is called containerization. Containers are not new, but their use for easily deploying applications is.

Containerization is increasingly popular because containers are:

- Flexible: Even the most complex applications can be containerized.
- Lightweight: Containers leverage and share the host kernel.
- Interchangeable: You can deploy updates and upgrades on-the-fly.
- Portable: You can build locally, deploy to the cloud, and run anywhere.
- Scalable: You can increase and automatically distribute container replicas.
- Stackable: You can stack services vertically and on-the-fly.

### Images and containers

A container is launched by running an image. An image is an executable package that includes everything needed to run an application--the code, a runtime, libraries, environment variables, and configuration files.

A container is a runtime instance of an image--what the image becomes in memory when executed (that is, an image with state, or a user process). You can see a list of your running containers with the command, docker ps, just as you would in Linux.

### Containers and virtual machines

A container runs natively on Linux and shares the kernel of the host machine with other containers. It runs a discrete process, taking no more memory than any other executable, making it lightweight.

By contrast, a virtual machine (VM) runs a full-blown “guest” operating system with virtual access to host resources through a hypervisor. In general, VMs provide an environment with more resources than most applications need.

![虚拟机](/assets/images/docker/VM@2x.png) ![容器](/assets/images/docker/Container@2x.png)


## 安装Docker

### OS requirements

To install Docker CE, you need a maintained version of CentOS 7. Archived versions aren’t supported or tested.

The centos-extras repository must be enabled. This repository is enabled by default, but if you have disabled it, you need to [re-enable it](https://wiki.centos.org/AdditionalResources/Repositories).

The overlay2 storage driver is recommended.

### Uninstall old versions

Older versions of Docker were called docker or docker-engine. If these are installed, uninstall them, along with associated dependencies.

```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```
It’s OK if yum reports that none of these packages are installed.

The contents of /var/lib/docker/, including images, containers, volumes, and networks, are preserved. The Docker CE package is now called docker-ce.


### Install Docker CE Using the repository

Before you install Docker CE for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

1. Install required packages. yum-utils provides the yum-config-manager utility, and device-mapper-persistent-data and lvm2 are required by the devicemapper storage driver.

```
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

2. Use the following command to set up the stable repository. You always need the stable repository, even if you want to install builds from the edge or test repositories as well.

```
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

3. Optional: Enable the edge and test repositories. These repositories are included in the docker.repo file above but are disabled by default. You can enable them alongside the stable repository.

```
$ sudo yum-config-manager --enable docker-ce-edge
$ sudo yum-config-manager --enable docker-ce-test
```

You can disable the edge or test repository by running the yum-config-manager command with the --disable flag. To re-enable it, use the --enable flag. The following command disables the edge repository.

```
$ sudo yum-config-manager --disable docker-ce-edge
```

> Note: Starting with Docker 17.06, stable releases are also pushed to the edge and test repositories.
> 

### INSTALL DOCKER CE

Install the latest version of Docker CE, or go to the next step to install a specific version:
```
$ sudo yum install docker-ce
````
If prompted to accept the GPG key, verify that the fingerprint matches 060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35, and if so, accept it.

> Got multiple Docker repositories?
> 
> If you have multiple Docker repositories enabled, installing or updating without specifying a version in the yum install or yum update command always installs the highest possible version, which may not be appropriate for your stability needs.

Docker is installed but not started. The docker group is created, but no users are added to the group.

2. To install a specific version of Docker CE, list the available versions in the repo, then select and install:

a. List and sort the versions available in your repo. This example sorts results by version number, highest to lowest, and is truncated:
```
$ yum list docker-ce --showduplicates | sort -r

docker-ce.x86_64            18.03.0.ce-1.el7.centos             docker-ce-stable
```

The list returned depends on which repositories are enabled, and is specific to your version of CentOS (indicated by the .el7 suffix in this example).

b. Install a specific version by its fully qualified package name, which is the package name (docker-ce) plus the version string (2nd column) up to the first hyphen, separated by a hyphen (-), for example, docker-ce-18.03.0.ce.
```
$ sudo yum install docker-ce-<VERSION STRING>
```
Docker is installed but not started. The docker group is created, but no users are added to the group.

3. Start Docker.
```
$ sudo systemctl start docker
```
4. Verify that docker is installed correctly by running the hello-world image.
```
$ sudo docker run hello-world
```
This command downloads a test image and runs it in a container. When the container runs, it prints an informational message and exits.

Docker CE is installed and running. You need to use sudo to run Docker commands. Continue to Linux postinstall to allow non-privileged users to run Docker commands and for other optional configuration steps.

## Test Docker version

1. Run docker --version and ensure that you have a supported version of Docker:
```
docker --version

Docker version 17.12.0-ce, build c97c6d6
```

2. Run docker info or (docker version without --) to view even more details about your docker installation:

```
docker info

Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 17.12.0-ce
Storage Driver: overlay2
...
```

> To avoid permission errors (and the use of sudo), add your user to the docker group. 

## Test Docker installation

1. Test that your installation works by running the simple Docker image, hello-world:

```
docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
ca4f61b1923c: Pull complete
Digest: sha256:ca0eeb6fb05351dfc8759c20733c91def84cb8007aa89a5bf606bc8b315b9fc7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```

2. List the hello-world image that was downloaded to your machine:
```
docker image ls
```

3. List the hello-world container (spawned by the image) which exits after displaying its message. If it were still running, you would not need the --all option:

```
docker container ls --all

CONTAINER ID     IMAGE           COMMAND      CREATED            STATUS
54f4984ed6a8     hello-world     "/hello"     20 seconds ago     Exited (0) 19 secon
```

## Recap and cheat sheet

```
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq

```

