# Introduction
Docker is an **open-source platform** that automates the **deployment**, **scaling**, and **management** of applications by isolating them into lightweight, portable containers. 

Containers are standalone executable units that encapsulate all necessary dependencies, libraries, and configuration files required for an application to run consistently across various environments.
## What Are Containers?
Containers are **lightweight**, **portable**, and **isolated** software environments that allow developers to run and package applications with their dependencies, consistently across different platforms. 

Containers help to streamline application development, deployment, and management processes while ensuring that applications run consistently, regardless of the underlying infrastructure.
## Why do we need Containers?
Containers solve the issue around **inconsistent environments** when working in large teams. Before containers or virtual environments, a lot of issues and time loss was caused by having to **install and configure local environments** to build projects **shared** by co-workers or friends.
## Bare metal VM vs VMs vs Containers
### TLDR
- **Containers**: Ideal for lightweight, scalable applications (e.g., microservices). Best for modern cloud-native development.
- **VMs**: Suitable for running multiple OS environments or legacy applications requiring high isolation.
- **Bare-metal**: Best for performance-intensive workloads, such as gaming servers or databases, where hardware is fully utilized.
---
* **Bare metal** is a term used to describe a computer that is running directly on the hardware without any virtualization. This is the most performant way to run an application, but it is also the least flexible. You can only run one application per server, and you cannot easily move the application to another server. 
* **Virtual machines (VMs)** are a way to run multiple applications on a single server. Each VM runs on top of a hypervisor, which is a piece of software that emulates the hardware of a computer. The hypervisor allows you to run multiple operating systems on a single server, and it also provides isolation between applications running on different VMs. 
* **Containers** are a way to run multiple applications on a single server without the overhead of a hypervisor. Each container runs on top of a container engine, which is a piece of software that emulates the operating system of a computer.

| Feature                      | Containers                                                                    | Virtual Machines (VMs)                                 | Bare-Metal Servers                                    |
| ---------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------ | ----------------------------------------------------- |
| **Definition**               | **Lightweight**, **isolated** environment sharing a host OS                   | **Fully virtualized systems** with independent **OS**. | **Physical hardware** dedicated to a single user.     |
| **Performance**              | **Near-native performance**, with minimal **overhead**.                       | **Moderate** due to hypervisor **overhead**.           | **Best performance**; no virtualization **overhead**. |
| **Resource** **Utilization** | **Highly efficient**; multiple containers share same resources.               | **Less efficient**; each VM uses it's own OS instance. | **Depends on workload**; full hardware resources.     |
| **Isolation**                | **OS-level isolation**; less secure than VMs because of shared underlying OS. | **High isolation** with hypervisors.                   | **Fully isolated.**                                   |
| **Deployment** **Speed**     | **Very fast**; containers are lightweight.                                    | **Moderate**; depends on VM size.                      | **Slow**; requires hardware setup.                    |
| **Scalability**              | **Highly scalable** for microservice architectures.                           | **Scalable** but slower than containers.               | **Limited**; scaling requires additional hardware.    |
| **Use Cases**                | Cloud-native apps, CI/CD pipelines, microservices.                            | Legacy apps, mixed OS environments.                    | High-performance computing, database servers.         |
| **Cost Efficiency**          | **Very cost-effective** due to efficient resource sharing.                    | **Moderate** due to higher resource demands.           | **Higher cost**; dedicated hardware.                  |
| **Management**               | Managed via **container orchestration** (i.e. **Kubernetes**).                | Managed via **hypervisors** (i.e. VMware, Hyper-V).    | Managed **directly** or with server management tools. |
* **Overhead** = Refers to the extra work or resources needed to make something function beyond its main purpose.
	* **In Computing**: happens when system uses part of its power or resources just to manage itself, instead of doing the main job you're asking for.
	* **For VMs**: This overhead comes from running a **hypervisor** (software that manages VMs) and having multiple OS's which take extra memory and CPU
	* **For Containers**: The overhead is smaller because they share the same OS and need fewer resources to manage themselves.
**In essence:**
* Think of it like carrying a backpack. 
	* A small, light backpack (low overhead, like containers) makes it easier to walk. 
	* A heavy one (high overhead, like VMs) slows you down.
## Docker and OCI
**The Open Container Initiative** (**OCI**) is a Linux Foundation project which aims at creating industry standards for container formats and runtimes. Its primary goal is to **ensure the compatibility** and **interoperability** of container environments through defined technical specifications.
# Underlying Technologies (Basic Idea)
Understanding the core technologies that power Docker will provide you with a deeper insight into how Docker works and will help you use the platform more effectively.
## Linux Containers (LXC)
Linux Containers (**LXC**) enables running multiple independent Linux systems on a single computer. Acting as isolated spaces, **LXC containers** share host resources like memory and processing power, without needing their own full operating system copy, ensuring lightweight and fast startup.
## Namespaces
Docker uses **namespaces** to create **isolated environments for containers**. They provide a layer of isolation by creating separate instances of global system resources, making each container believe it has its own unique set of resources.

Docker utilizes several types of namespaces, including **PID** (Process ID), **NET** (Network), **MNT** (Mount), **UTS** (Unix Timesharing System), **IPC** (InterProcess Communication), and **USER** namespaces and by leveraging these namespaces, Docker can create lightweight, portable, and secure containers that run consistently across different environments.
## Control Groups (cgroups)
cgroups or “``control groups``” are a Linux kernel feature that allows you to **allocate and manage resources**, such as **CPU**, **memory**, **network bandwidth**, and **I/O**, among groups of processes running on a system. 
1. **Limit resources**: Set upper bounds on resource usage (e.g., maximum memory or CPU shares).
2. **Prioritize resources**: Allocate higher priority to some processes over others.
3. **Account for resources**: Monitor and track how much resources are used.
4. **Isolate resources**: Ensure that processes in one group do not interfere with those in another.
## Union Filesystems
A **Union Filesystem** or ``UnionFS`` is a filesystem service that allows multiple directories (often called layers) to be overlaid into a **single unified view**. It presents the contents of these directories as if they were one, without merging the actual files and directories on disk.
### Role of Union Filesystems in Docker
Docker uses union filesystems to manage **images** and **containers** efficiently by taking advantage of their layering capability.
1. **Image Layers**:
    - Docker images are built in layers. Each layer represents a set of changes (e.g., adding a file, installing software) made on top of the previous layer.
    - The union filesystem overlays these layers to create a single, unified view of the filesystem.
2. **Copy-on-Write (COW)**:
    - When you create a container from an image, Docker doesn't duplicate the entire image. Instead, it uses a **Copy-on-Write** mechanism:
        - The container initially shares the read-only image layers.
        - Any changes made by the container are written to a new, writable layer specific to that container.
3. **Efficiency**:
    - Multiple containers can share the same image layers, which saves disk space.
    - Changes made by one container do not affect others because they operate on separate writable layers.
# Installation / Setup
Docker provides a desktop application called ``Docker Desktop`` that simplifies the installation and setup process. 

There is also another option to install using the ``Docker Engine`` but be aware that installing just the Docker Engine will not provide any **GUI**.

## Docker Desktop (Win / Mac / Linux)
* An easy-to-install application that enables developers to quickly set up a Docker environment on their desktop machines. 
* Available for *Windows*, *MacOS*, and *Linux*. 
* Designed to simplify the process of managing and running Docker containers, providing a user-friendly interface and seamless integration with the host operating system.
## Docker Engine (Linux)
There is often confusion between “Docker Desktop” and “Docker Engine”. 
* **Docker Engine** refers specifically to a subset of the Docker Desktop components which are free and open source and can be installed only on Linux.
* **Docker Engine** can **build container images, run containers from them**, and **generally do most things that Docker Desktop can**
* Linux only and doesn’t provide all of the developer experience polish that Docker Desktop provides.

# Basics of Docker
Docker is a platform that simplifies the process of **building**, **packaging**, and **deploying** applications in lightweight, portable containers. Here, we’ll cover docker basics, the components, and key commands.
## What is a Container?
* A container
	* Is lightweight
	* Is Standalone
	* An executable package of software 
	* Includes all the dependencies (**libraries**, **binaries**, and **configuration files**) required to run an application. 
* Containers isolate applications from their environment, ensuring they work consistently across different systems.
## Docker Components
There are three key components in the Docker ecosystem:
- **Dockerfile**: A text file containing instructions (commands) to build a Docker image.
- **Docker Image**: A snapshot of a container, created from a Dockerfile. Images are stored in a registry, like Docker Hub, and can be pulled or pushed to the registry.
- **Docker Container**: A running instance of a Docker image.
## Docker Commands
Below are some **essential Docker commands** you’ll use frequently:
- `docker pull <image>`: Download an image from a registry, like **Docker Hub**.
- `docker build -t <image_name> <path>`: Build an image from a Dockerfile, where `<path>` is the directory containing the Dockerfile.
- `docker image ls`: List all images available on your local machine.
- `docker run -d -p <host_port>:<container_port> --name <container_name> <image>`: Run a container from an image, mapping host ports to container ports.
- `docker container ls`: List all running containers.
- `docker container stop <container>`: Stop a running container.
- `docker container rm <container>`: Remove a stopped container.
- `docker image rm <image>`: Remove an image from your local machine.
# Data Persistence
* Docker enables you to run containers that are isolated pieces of code, including applications and their dependencies, separated from the host operating system. 
* Containers are ephemeral ("_lasting for a very short time_") by default, which means any data stored in the container will be lost once it is terminated. 
* To overcome this problem and retain data across container lifecycle, Docker provides various data persistence methods which are discussed below.
## Ephemeral container filesystem
By default, the storage within a Docker container is **ephemeral**, meaning that any data changes or modifications made inside a container **will only persist as long as the container is running**. Once the container is stopped and removed, all the associated data will be lost. This is because Docker containers are designed to be stateless by nature. This temporary or short-lived storage is called the “``ephemeral container file system``”. It is an essential feature of Docker, as it enables fast and consistent deployment of applications across different environments without worrying about the state of a container.
## Volume Mounts
* Volume mounts are a way to map a folder or file on the host system to a folder or file inside a container. 
* This allows the data to **persist outside the container** even when the container is removed. 
* Additionally, **multiple containers can share the same volume**, making data sharing between containers easy.
## Bind Mounts
* Bind mounts have limited functionality compared to volumes. 
* When you use a **bind mount**, a **file** or **directory** on **the host machine** is mounted into a container. 
* The file or directory is referenced by its **absolute path** on the host machine. 
* By contrast, when you use a volume, a new directory is created within Docker’s storage directory on the host machine, and Docker manages that directory’s contents.
# Using 3rd Party Container Images
* Third-party images are **pre-built Docker container images** that are available on Docker Hub or other container registries. 
* These images are created and maintained by individuals or organizations and can be used as a starting point for your containerized applications.
## Databases
* Running your **database** in a **Docker container** can help streamline your development process and ease deployment. 
* Docker Hub provides numerous **pre-made images** for popular databases such as ``MySQL``, ``PostgreSQL``, and ``MongoDB``.
## Interactive Test Environments
* Docker allows you to create **isolated**, **disposable** environments that can be deleted once you’re done with testing. 
* This makes it much easier to work with third party software, test different dependencies or versions, and quickly experiment without the risk of damaging your local setup.
## Command Line Utilities
* Docker images can include command line utilities or standalone applications that we can run inside containers.
# Building Container Images
* Container images are executable packages that include everything required to run an application: ``code``, ``runtime``, ``system tools``, ``libraries``, and ``settings``. 
* By building **custom images**, you can deploy applications seamlessly with all their dependencies on any Docker-supported platform. 
* The key component in building a container image is the `Dockerfile`. It is essentially a script containing instructions on how to assemble a Docker image. 
* Each instruction in the ``Dockerfile`` creates a **new layer** in the image, making it easier to track changes and minimize the image size. Here’s a simple example of a Dockerfile:
* ![[1_wDC9bAk_EJP4D_6pU5VNgQ.png]]
## Dockerfiles
* A Dockerfile is a **text document**.  
* Contains a list of instructions used by the **Docker engine** to **build an image**.
* Each instruction in the **Dockerfile** adds a new layer to the image. 
* Docker will build the image based on these instructions, and then you can run containers from the image.
## Efficient layer caching
* When building container images, Docker caches the newly created layers. 
* These layers can then be used later on when building other images, reducing the build time and minimizing bandwidth usage.
* However, to make the most of this caching mechanism, you should be aware of how to efficiently use layer caching. 
* Docker creates a new layer for each instruction (e.g., `RUN`, `COPY`, `ADD`, etc.) in the Dockerfile. 
* If the instruction **hasn’t changed** since the last build, Docker will reuse the existing layer.
## Reducing Image Size
* Reducing Docker image size is crucial for optimizing storage, transfer speeds, and deployment times. 
* Key strategies include:
	* **Using minimal base images**, like [Alpine Linux](https://alpinelinux.org/), 
	* **Leveraging multi-stage builds**, to exclude unnecessary build tools,
	* **Removing unnecessary files and packages**,
	* **Minimizing the number of layers** by combining commands.

# Container Registries
* A Container Registry is a **centralized storage and distribution system** for Docker container images. 
* It allows developers to easily share and deploy applications in the form of these images.
* Container registries play a crucial role in the deployment of containerized applications, as they provide a **fast**, **reliable**, and **secure way to distribute container images** across various production environments.
**Example Registries**:
* [Docker Hub](https://hub.docker.com/)
* [Amazon ECR](https://aws.amazon.com/ecr/)
* [Harbor](https://goharbor.io/)
## Dockerhub
* [Docker Hub](https://hub.docker.com/) is a cloud-based registry service that serves as the primary public repository for Docker container images. 
* Allows users to **store**, **share**, and **distribute Docker images**, offering both free public repositories and paid private ones and integrates seamlessly with **Docker CLI**, enabling easy **pushing** and **pulling** of images. 
* It features **official images maintained by software vendors**, automated builds linked to source code repositories, and webhooks for triggering actions based on repository events.
## Others (ghcr, ecr, gcr, act, etc)
* Container images can be stored in many different registries, not just [Docker Hub](https://hub.docker.com/). 
* Most major cloud platforms now provide container registries such as
	* **Artifact Registry** on Google Cloud Platform, 
	* **Elastic Container Registry** on AWS 
	* **Azure Container Registry** on Microsoft Azure. 
	* **GitHub** also provides it’s own registry which is useful when container builds are included in your GitHub Actions workflow.
## Image Tagging Best Practices
* Docker image tagging best practices centre on **creating clear, consistent, and informative labels**. 
* **Adopt semantic versioning for releases**, avoid the ambiguous “latest” tag in production, and include relevant metadata like build dates or Git commit hashes. 
* **Implement a strategy distinguishing between environments**, use descriptive tags for variants, and **automate tagging** in CI/CD pipelines.
* **Regularly clean up old tags and document your conventions** to maintain clarity and facilitate team-wide adoption. 
These practices ensure **efficient image management** and **improve collaboration** across your organization.
# Running Containers
Running docker containers is typically done with a simple: `docker run` command, which is a combination of the `docker create` and `docker start` commands.
## Docker run
* The `docker run` command creates and starts a new container from a specified image.
* It combines `docker create` and `docker start` operations, offering a range of options to customize the container’s runtime environment. 
* Users can set environment variables, map ports and volumes, define network connections, and specify resource limits. 
* The command supports detached mode for background execution, interactive mode for shell access, and the ability to override the default command defined in the image. 
* Common flags include:
	* `-d` for detached mode.
	* `-p` for port mapping.
	* `-v` for volume mounting.
	* `--name` for assigning a custom container name. 
Understanding `docker run` is fundamental to effectively deploying and managing Docker containers.
## Docker compose
* Docker Compose is a tool for defining and running multi-container Docker applications.
* It allows you to create, manage, and run your applications using a simple YAML file called `docker-compose.yml`. 
* ``docker-compose.yml`` describes your application’s services, networks, and volumes, allowing you to easily run and manage your containers using just a single command.
**Example `docker-compose.yml`**:
```yaml
version: '3.9'
services:
  app:
    build:
      context: .
    ports:
      - "3000:3000"
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
```
**What it does**:
- Defines two services: `app` and `db`.
- The `app` service is built from a `Dockerfile` in the current directory.
- Ensures the `db` service (**PostgreSQL**) is available before starting `app`.
## Runtime Configuration Options
* Docker runtime configuration options give you powerful control over your containers’ environments. 
* By tweaking **resource limits**, **network settings**, **security profiles**, and **logging drivers**, you can **optimize performance** and **enhance security**.
* You’ll also find options for **setting environment variables, mounting volumes, and overriding default behaviours** – all crucial for tailoring containers to your specific needs.
* For more advanced users, there are tools to **adjust kernel capabilities and set restart policies**.
Whether you’re using **[[#Docker CLI|command line]] flags** or [[#Docker compose]], these options help ensure your containers run smoothly and consistently, no matter where they’re deployed.
# Container Security


## Runtime Security


## Image Security


# Docker CLI


## Images


## Containers


## Volumes

## Networks

# Developer Experience
## Hot Reloading

## Debuggers

## Tests

## Continuous Integration


# Deploying Containers

## PaaS Options

## Kubernetes

## Docker Swarm

# Nomad

