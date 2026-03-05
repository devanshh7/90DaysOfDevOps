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

-----------------------------------------------

# Install Docker

*Command:*

`sudo apt install docker.io -y`

*Verify: *

`docker --version`
<img width="611" height="158" alt="image" src="https://github.com/user-attachments/assets/0c64ac7a-7fb2-4222-8ebf-ba68c72bee79" />

*RUn hello-world*

`docker run hello-world`
<img width="761" height="470" alt="image" src="https://github.com/user-attachments/assets/a65a9bb3-b5ca-4d6e-a9dc-27f5edf698a7" />

**docker run creates or run a container from docker image if image not present it will extract from docker registry.**

**So, in the above hello-world is etracted an drun from the dockerhub**

---------------------------------

# Run real container

Run an **Nginx** container and run on your browser

`sudo apt install nginx`

`docker pull nginx` : wil pull the nginx img

`docker run -d -p 800:80 --name nginx-container nginx`:: ensure port binding 800 to 80 now you can use: http://YOUR-EC2-PUBLIC-IP:8080

<img width="728" height="131" alt="image" src="https://github.com/user-attachments/assets/2d7a4316-764f-4c54-a11f-01e316c8e5ee" />

<img width="743" height="364" alt="image" src="https://github.com/user-attachments/assets/2e42f53f-e33b-434a-b6e8-fa1d7865e6e1" />

**To Stop and remove container**

`docker stop cont-id && docker rm id`
<img width="716" height="193" alt="image" src="https://github.com/user-attachments/assets/3bb3c6a7-21c0-4471-a2d4-fa5dffaeb57d" />

-------

* **What is Detached Mode?**

Detached mode means the container runs in the background, and your terminal is free to run other commands.

Without detached mode, the container runs in the foreground and your terminal stays attached.

* **Give a Container a Custom Name**
`docker run -d --name my-nginx nginx`

* **Check Logs of a Running Container**

Logs show application output inside the container.

`docker logs container_name`

*Follow logs live*

`docker logs -f webserver`
