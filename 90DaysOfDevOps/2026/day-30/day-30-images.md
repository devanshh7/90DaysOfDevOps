# Docker Images & Container Lifecycle

**Pull Images**
```
docker pull nginx/ubuntu/alpine
```

**List Images**
`docker images`

**Compare ubuntu vs alpine — why is one much smaller?**

Alpine image is smaller as compared to ubuntu.

Key reasons

1️⃣ musl instead of glibc

* Alpine uses musl libc, a lightweight C standard library.

* glibc (used by Ubuntu) is bigger but more compatible.

2️⃣ BusyBox replaces many tools

* Alpine bundles many commands (ls, cp, cat, etc.) into one binary using BusyBox.

* Ubuntu installs separate GNU utilities.

3️⃣ Minimal base system

* Alpine installs only the essentials.

* Ubuntu includes many standard libraries and utilities by default.

4️⃣ Designed for containers

* Alpine was intentionally designed to be tiny and secure.

**Inspect image**
`docker inspect img-name` : When you inspect a container image, you can see a lot of metadata about how it was built and how it will run.

**Remove img**
`docker rmi <img-name>`

---------

**To see history of Service...**
`docker image history nginx` : command displays the layer-by-layer history of a Docker image, including the commands used to create each layer, their size, and creation time. This is useful for debugging, security auditing, and optimizing image size.


**Each line is a layer. What are layers and why does Docker use them?**

A Docker image is composed of multiple layers stacked on top of each other. Each layer represents a specific modification to the file system (inside the container), such as adding a new file or modifying an existing one. Once a layer is created, it becomes immutable, meaning it can't be changed. The layers of a Docker image are stored in the Docker engine's cache, which ensures the efficient creation of Docker images.

Examples of instructions that create layers:

* RUN
* COPY
* ADD
* FROM

**Building a Docker Image**
```
FROM alpine
LABEL maintainer=kodekloud
RUN apk update && apk add curl
ENTRYPOINT ["curl"]
CMD ["--version"]
```
A Dockerfile is a text file that contains a set of instructions for building a Docker image. Our example Dockerfile specifies the following instructions:

* FROM alpine: Sets the base image as alpine, a lightweight Linux distribution.
* LABEL maintainer=kodekloud: Assign metadata to the image with a maintainer key, which in this case is set to kodekloud.
* RUN apk update && apk add curl: Updates the package list and installs the curl command-line tool using the apk package manager.
* ENTRYPOINT ["curl"]: Sets the default executable for the container as curl.
* CMD ["--version"]: Specifies the default argument for the ENTRYPOINT, which, in this case, is --version.

<img width="1000" height="313" alt="image" src="https://github.com/user-attachments/assets/441013f7-35db-42a0-8e89-2a06c3200f89" />

Notice the two lines that start with [1/2] and [2/2]. The numbers indicate the current step and the total number of build steps involved in the build process. In our case, we had a 2-step build process. In step 1, Docker fetched and set up the base image alpine as specified in our Dockerfile by the FROM alpine command. In step 2, Docker executed the RUN apk update && apk add curl command as specified in our Dockerfile. This command updated the package list and installed the curl utility in the image.

**The two build steps above created two separate image layers.** What about the other instructions in our Dockerfile? Didn't they create any image layers? 

_Yes, they did, but they created intermediate layers. Intermediate layers are 0B in size and don't add to the image size (more on this later)._

As a general rule, **any Dockerfile instruction that modifies the file system creates a new layer.**

## Why Docker Uses Layers
Docker uses layers for efficiency and reuse.

* **Image Caching (Faster Builds)**

If a layer hasn’t changed, Docker reuses the cached layer instead of rebuilding.

* **Shared Layers Between Images**

Multiple images can share the same base layers.

Many images share layers from **Ubuntu or Alpine Linux.**

This saves disk space.

Example:
```
python:3.11

node:20

nginx
```

* **Faster Downloads**

When pulling images:

`docker pull nginx`

Docker downloads only layers you don’t already have.


---------------

## COntainer Lifecycle

***Create a container (without starting it),
Start the container,
Pause it and check status,
Unpause it,
Stop it,
Restart it,
Kill it,
Remove it***

```
docker pull nginx
```

```
docker create --name lifecycle-nginx nginx
```

* to check status
```
docker ps -a 
```

* Start the container
```
docker start lifecycle-nginx
```

* Pause the container
```
docker pause lifecycle-nginx
```

* Unpause the container
```
docker unpause lifecycle-nginx
```

* Stop the container
```
docker stop lifecycle-nginx
```

* Restart the container
```
docker restart lifecycle-nginx
```

* Kill the container
```
docker kill lifecycle-nginx
```

* Remove the container
```
docker rm lifecycle-nginx
```
--------------

## Working with Running Containers

_Detached mode_

- `docker run -d --name web -p 8080:80 nginx` : Detached mode = run in background.

- `docker logs web` : Shows stdout/stderr logs.
- `docker logs -f web` : Real-time logs (follow mode)
- `docker exec -it web /bin/bash` : Exec into the container
- `docker exec web ls /usr/share/nginx/html` : Run a single command inside container,
Without opening a shell
- `docker inspect web` : nspect the container — find its IP address, port mappings, and mounts

------------

**Stop all running containers in one command**
```
docker stop $(docker ps -q)
```
This command works by using command substitution to pass all running container IDs to the docker stop command. 

docker ps -q: This command lists all currently running containers (docker ps) and the -q (quiet) flag restricts the output to only their numeric IDs.

**Remove all stopped containers in one command**
`docker container prune`

**docker container prune**
`docker image prune`

**Check Docker disk usage**
`docker system df`
