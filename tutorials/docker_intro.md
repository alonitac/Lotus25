# Docker Containers Intro

## Containers terminology

A **Container Image** (or shortly, an **Image**) is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.
You can transfer images from one machine to the other. Every machine is able to run this image without the need to install the application dependencies, define environment variables and networking settings.

A **Container** is a single running instance of an image. You can create, start, stop, move, or delete a container, and they can be run easily and reliably from one computing environment to another.
The computer that runs the container is frequently referred to as a **Host Machine**, because it "hosts" containers.

By default, a container is relatively well isolated from other containers and its host machine. You can control how isolated a container's network, storage, or other underlying subsystems are from other containers or from the host machine.

A container, as we mentioned, is defined by its image as well as any configuration options you provide to it when you create or start it.
When a container is removed, any changes to its state that are not stored in persistent storage disappear.

## Containers under the hoods

Under the hoods, containers are merely a **linux processes**. 

But they are unique processes that "live" in an isolated environment. By this means, the process by default cannot "see" or interact any other process in the host machine. It "believes" that he is the only process in the system. 
Containers are a technology that leverages the Linux kernel's features to provide lightweight and isolated environments for running applications. 

## Docker architecture

**Docker** is an open platform for developing, building and shipping images, and running containers.

Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the work of building, running, and distributing your Docker containers. The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface.

![][docker_arc]

- The Docker daemon (`dockerd`) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes.
- The Docker client (`docker`) is the primary way that many Docker users interact with Docker. When you use commands such as docker run, the client sends these commands to dockerd, which carries them out.
- A Docker registry stores Docker images. [Docker Hub](https://hub.docker.com/) is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default. You will even run your own private registry later on in the course.

### Docker installation

To install Docker, please **carefully** follow the [installation instructions](https://docs.docker.com/desktop/setup/install/windows-install/) if you haven't done it before.

Upon up and running `docker` installation, the `docker info` command output may look like: 

```text
Client: Docker Engine - Community
 Version:           20.10.22
 API version:       1.41
 Go version:        go1.18.9
 Git commit:        3a2c30b
 Built:             Thu Dec 15 22:28:02 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.22
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.9
  Git commit:       42c8b31
  Built:            Thu Dec 15 22:25:51 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.14
  GitCommit:        9ba4b250366a5ddde94bb7c9d1def331423aa323
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

## Hello world example 

The Docker "Hello World" container is a simple and lightweight container that is often used to verify if Docker is properly installed and functioning on a system. It is based on the official Docker image called [hello-world](https://hub.docker.com/_/hello-world).

```bash
docker run hello-world
```

The hello-world image is an example of minimal containerization with Docker.
It has a single `hello.c` file responsible for printing out the message you're seeing on your terminal.

[docker_arc]: https://exit-zero-academy.github.io/DevOpsTheHardWayAssets/img/docker_arc.png
