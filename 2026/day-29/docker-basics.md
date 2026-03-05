# Introduction to Docker

### Task 1: What is Docker?

Research and write short notes on:

* What is a container and why do we need them?
* Containers vs Virtual Machines — what's the real difference?
* What is the Docker architecture? (daemon, client, images, containers, registry)

1. Container are standalone , lightweight exe package where all the appliation libraries,code,runtime , dependencies are wrapped inside a container.

   container can be shipped an d run to any instance or system irrespective of software or OS it runs same across all environment.

It ensures:
  * Portability
  * Consistency

2. Containers share the host operating system kernel and isolate applications using namespaces and cgroups.

Multiple containers run on the same OS.

Advantages

  * Lightweight
  * Fast startup
  * Easy scaling

Ideal for microservices

  * Limitation
  *  Weaker isolation than VMs
  * Must use same OS kernel as host

VMs virtualize entire hardware using a hypervisor.

Each VM includes a full operating system.

VMs are used for:

* running different operating systems
* strong isolation
* legacy workloads

Containers are used for:

* microservices
* CI/CD pipelines
* cloud-native apps
* scalable deployments with Kubernetes

3.  DOCKER ARCHITECTURE (Deamon, client, containers, refistry)

   The architecture of Docker follows a client-server model.

  * DOCKER CLIENT

    The Docker client is the command line interface used to interact with Docker.
      ```
      docker build
      docker pull
      docker run
      docker ps
      ```
    
  * Docker Daemon

    The Docker daemon (dockerd) is the background service that:

    * builds images

    * runs containers

    * manages networks

    * manages volumes

      It listens for Docker API requests from the client.

  * DOCKER IMAGES

    A Docker image is a read-only template used to create containers.

    Images contain:

    * application code

    * dependencies

    * runtime

    * environment

      ```
      docker pull nginx
      ```
  
  * Docker Containers

    A container is a running instance of an image.

    Containers are:

    lightweight

    isolated

    portable
