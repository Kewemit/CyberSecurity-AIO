# docker-katas
## 00 - Installing Docker - Linux
### Docker terminology
Throughout these notes, we will see a lot of Docker-specific _jargon_ which might be confusing. So let's clarify some terminology that is used frequently in the Docker ecosystem.
* `Docker Container`: An isolated, runnable environment that holds everything needed to run an application. 
* `Docker Image`: A lightweight, standalone package that contains all necessary code, libraries, and dependencies to run an application. Think of an image like a **blueprint** or **snapshot** of what will be in a **container** when it runs.
* `Docker daemon`: The background service running on the host that manages building, running and distributing Docker containers.
* `Docker client`: The CLI tool that allows the user to interact with the `Docker daemon`.
* `Docker Hub`: A [Docker registry](https://hub.docker.com/) of **Docker images**. The registry is like a directory of all available Docker images.
### Installation on Kali
To install **Docker** on **Kali** we need to remember that there is already a package named “``docker``”, therefore Docker has to be installed under a different name.  The version we will be installing is named `docker.io`. All commands are the same however, so running `docker` on the command line will be the appropriate command:
```bash
$ sudo apt update # Update our download index to get latest software
$ sudo apt install -y docker.io # Install Docker
$ sudo systemctl enable docker --now # Enable the Docker service
$ docker # Run docker
```
Then to get started, let's add ourselves to the docker group:
```bash
$ sudo usermod -aG docker $USER # Add us to the Docker group
```

## 01 - hello-world
### The Goal
The goal here is to make us **run our first Docker container**.
### Terminology
`Docker Container`: An isolated, runnable environment that holds everything needed to run an application. 
`Docker Image`: A lightweight, standalone package that contains all necessary code, libraries, and dependencies to run an application.
### Exercise
Let's try running this command with Docker:
```bash
$ docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete 
Digest: sha256:5b3cc85e16e3058003c13b7821318369dad01dac3dbb877aac3c28182255c724
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

```
##### Q: So what did this do?
If we try to run `docker run hello-world` again, we can see we can execute it straight away. Docker has now downloaded the image locally so we can run the container straight away. 
## 02 - Running Images
#### Learning Goals
* Run an [Alpine Linux](https://www.alpinelinux.org/) container (a lightweight Linux distribution) and get a taste for the `Docker run` command.
#### Exercise
To get started with our first **container** from an **image**:
* We'll first pull the Alpine Linux image. 
* Then we explore various commands to interact with it.
#### Overview
- Pull the Alpine Linux image. | `$ docker pull alpine`
- List all images on your system. | `$ docker image list`
- Run a Docker container based on the Alpine image. | `$ docker run -it alpine`
- Explore various commands inside the container. 
- Understand container naming and IDs.
#### Step By Step
To get started, let's run this in the terminal:
* `$ docker pull alpine`
The `pull` command in docker fetches the alpine **image** from the **Docker registry** and saves it in our system. To list our images, we can use:
* `$ docker image ls`
Expected output:
```
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
alpine        latest    4048db5d3672   3 weeks ago     7.83MB
ubuntu        latest    b1d9df8ab815   5 weeks ago     78.1MB
hello-world   latest    d2c94e258dcb   20 months ago   13.3kB

```
#### 1.1 docker run
Let's run Docker **container** based on this image:
* `$ docker run alpine ls -l`
Expected output:
```bash
total 48
drwxr-xr-x    2 root     root          4096 Mar  2 16:20 bin
drwxr-xr-x    5 root     root           360 Mar 18 09:47 dev
drwxr-xr-x   13 root     root          4096 Mar 18 09:47 etc
drwxr-xr-x    2 root     root          4096 Mar  2 16:20 home
drwxr-xr-x    5 root     root          4096 Mar  2 16:20 lib
...SNIP...
```
When we run `docker run alpine`, we provided a command (`ls -l`), so Docker started the command specified and we saw the listing. 

Let's try the following:
* `$ docker run alpine echo "hello from alpine"`
Expected output:
```bash
hello from alpine
```
In this case, the **Docker client** ran the `echo` command in our alpine container and then exited it. If you've noticed, all of that happened pretty quickly. Imagine: 
1. Booting up a virtual machine.
2. Running a command.
3. And then killing it. 
Now you know why they say containers are fast!

Let's try another command:
* `$ docker run alpine /bin/sh`
We can see that nothing happened. This is because these interactive shells will exit after running any scripted commands, unless they are run in an **interactive terminal**. So for example to not exit, we need to add the parameters `i` and `t`.
>[!important] These flags `-it` are short for `-i -t` which are short for `--interactive` (Keep the STDIN open) and `--tty` (Allocate a terminal).

* `$ docker run -it alpine /bin/sh`
We are now inside a container shell, and we can try out a few commands like: `ls -l`, `uname -a` and others.
* To exit out of the container, we can write `exit`.

Now it's time to list our containers. We can list all the containers that are **currently running** using the `docker ps` command.
* `$ docker ps`
Expected output:
```bash
CONTAINER ID        IMAGE               COMMAND             CREATED             


STATUS              PORTS               NAMES
```
We can see that we have no currently running containers. When we wrote `exit` in the shell, the primary process, which was `/bin/sh` stopped.

We can also list all containers, started and stopped:
* `$ docker ps -a`
Expected output:
```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
36171a5da744        alpine              "/bin/sh"                5 minutes ago       Exited (0) 2 minutes ago                        fervent_newton
a6a9d46d0b2f        alpine              "echo 'hello from alp"   6 minutes ago       Exited (0) 6 minutes ago                        lonely_kilby
ff0a5c3750b9        alpine              "ls -l"                  8 minutes ago       Exited (0) 8 minutes ago                        elated_ramanujan
c317d0a9e3d2        hello-world         "/hello"                 34 seconds ago      Exited (0) 12 minutes ago                       stupefied_mcclintock
```
What we see above is a list of all containers that we ran. Notice that the `STATUS` column shows that these containers exited a few minutes ago.

#### Naming a container
If we look again at the output of:
```bash
$ docker ps -a
```
We get the output of:
```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
36171a5da744        alpine              "/bin/sh"                5 minutes ago       Exited (0) 2 minutes ago                        fervent_newton
a6a9d46d0b2f        alpine              "echo 'hello from alp"   6 minutes ago       Exited (0) 6 minutes ago                        lonely_kilby
ff0a5c3750b9        alpine              "ls -l"                  8 minutes ago       Exited (0) 8 minutes ago                        elated_ramanujan
c317d0a9e3d2        hello-world         "/hello"                 34 seconds ago      Exited (0) 12 minutes ago                       stupefied_mcclintock
```
All containers have an **ID** and a **name**. Both the ID and the name is generated every time a new container spins up with a random seed for uniqueness.

If we wanted to give the container a specific name, we could use the `--name` option.

## 03 - Deletion
### Throw the container away
As containers are just a thin base layer on top of the host kernel, it is really fast to spin up a new instance if we crashed our old one.

Let's try to run an alpine container and delete the file system.

Spin up the container with `$ docker run -ti alpine` and then list all the folders on the root level to see the whole distribution:
```bash
$ whoami
root

$ ls /
bin    etc    lib    mnt    proc   run    srv    tmp    var
dev    home   media  opt    root   sbin   sys    usr
```

Now, let's delete the binaries that the system is build up with:
```bash
$ rm -rf /bin
```
Try to navigate around to see how much of the OS is gone: try to run the `ls`, `whoami` and `date` commands. They should all echo back that the binary is not found.

We can exit out the docker by pressing `ctrl+d` and create a new instance and look around:
```bash
$ ls -la
bin    etc    lib    mnt    root   sbin   sys    usr
dev    home   media  proc   run    srv    tmp    var
```
We can see that we got a new OS with just one command.

### Auto-remove container after use
Every time we create a new container, it will take up some space. To see what containers are taking space, let's run the command below:
```bash
$ docker container ls -as                                                         NAMES               SIZE
4b09b2fe1d8c        alpine                    "/bin/sh"    ...SNIP
```
Here we can see that the alpine image itself takes around ``~3.97MB``, and the container itself takes ``0B``.  (Not shown in the output above)

When we begin to manipulate files in the container, the size of the container will also rise.

If we are creating a lot of new containers (i.e. to test something), we can tell the Docker daemon to remove the container once stopped with the `--rm` option:
```bash
$ docker run --rm -it alpine
```
This flag will remove the container immediately after it is stopped.
### Cleaning up un-used containers
Containers are still persisted, even though they are stopped. If we want to delete them from our server, we can use the `docker rm` command. `docker rm` can take either the `CONTAINER ID` or `NAME`.

We can remove the `hello-world` container with:
```bash
$ docker container ls -a
0821808fdb67   hello-world   "/hello"  magical_dijkstra

$ docker container rm magical_dijkstra
magical_dijkstra
```
### Deleting images
We deleted the container instance above, but not the **image** of ``hello-world`` itself. And as we don't need it anymore, we can delete it with:
```bash
$ docker image rm hello-world # Or alternatively use the ID
Untagged: hello-world:latest
Untagged: hello-world@sha256:5b3cc85e16e3058003c13b7821318369dad01dac3dbb877aac3c28182255c724
Deleted: sha256:d2c94e258dcb3c5ac2798d32e1249e42ef01cba4841c2234249495f87264ac5a
Deleted: sha256:ac28800ec8bb38d5c35b49d45a6ac4777544941199075dff8c4eb63e093aa81e
```
What docker did here was to `untag` the image removing the references to the sha of the image. After the image has no references, it deletes the two layers the image itself is comprised of.
### Cleaning up
When **building**, **running** and **rebuilding images**, you download and store a lot of layers. These layers will not be deleted, as docker takes a very conservative approach to clean up.