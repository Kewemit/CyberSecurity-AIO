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

Docker provides the `prune` command, taking all dangling containers/images/networks/volumes.
* `docker container prune`: To delete all containers
* `docker image prune`: To delete all images
* `docker network prune`: To delete all networks
* `docker volume prune`: To delete all volumes

The ``docker image prune`` command allows you to clean up unused images. By default, docker image prune only cleans up dangling images. A dangling image is one that is **not tagged** and is **not referenced** by any container. To remove all **_unused_** resources, resources that are not directly used by any existing containers, use the `-a` switch as well.

If you want a general clean-up, then `docker system prune` is your friend.
### Summary 
You have now seen the swiftness of **creating** a new container from an image, **trashing** it, and create a new one on top of it. You have learned to use `container rm` for deleting containers, `image rm` for images, `image ls` for listing the images and `container ls -a` to look at all the containers on your host.
## 04 - Port Forwarding
Pull the `nginx` Docker image from the Docker Hub. This Docker image uses the [Nginx](http://nginx.org/) webserver to serve a **static HTML website**.

Start a new container from the `nginx` image that exposes port ``80`` from the container to port ``8080`` on your host. You will need to use the `-p` flag with the docker container run command.

Mapping ports between your host machine and your containers can get confusing. Here is the syntax you will use:
```bash
$ docker run -p 8080:80 nginx
```
>[!important] The trick is to remember that **the host port always goes to the left**, and **the container port always goes to the right.** Remember it as traffic coming _from_ the host, _to_ the container.

If we open a web browser and go to port ``8080`` on your host. The exact address will depend on how you're running Docker today:
* **Native Linux**: `http://localhost:8080`
* **Cloud Server**: Make sure firewall is configured to allow traffic on port ``8080``. Open browser and use the **hostname** (or **IP**) for your server. Ex: `http://inst1.prefix.eficode.academy:8080` - Alternatively open a new shell and issue `curl localhost:8080`
* **Google Cloud Shell**: `Open Web Preview (upper right corner)`

If you see a webpage saying "``Welcome to nginx!``" then you're done!

If you look at the console output from **docker**, you see **nginx** producing a line of text for each time a browser hits the webpage, just like normally:
```bash
$ docker run -p 8080:80 nginx

172.17.0.1 - - [31/May/2017:11:52:48 +0000] "GET / HTTP/1.1" 200 612 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:53.0) Gecko/20100101 Firefox/53.0" "-
```
Press **Ctrl + C** in your terminal window to stop your container.

### Running the webserver container in the background
When running a webserver like nginx, it is very useful to not run the container in the foreground of our terminal. Docker enables this with the `-d` parameter for the `run` command. For example: 
```bash
$ docker run -d -p 8080:80 nginx
```
Docker prints out the container ID and returns to the terminal.

Now, we know how to run a container in the background.
### Clean-up
Stop the container you just started. Remember that your container ID is different from the one in the example.
```bash
$ docker stop 78c943461b49584ebdf841f36d113567540ae460387bbd7b2f885343e7ad7554

# Docker then prints out the ID of the stopped container
78c943461b49584ebdf841f36d113567540ae460387bbd7b2f885343e7ad7554
```
## 05 - Executing processes in your container
It you want to examine a running container, but do not want to disturb the running process you can **execute another process inside the container** with `exec`.

This could be a **shell**, or **a script** of some sort. In that way you can debug an existing environment before starting a new up.
### Exercise
In this exercise, we want to change a file in an already running container, by executing a secondary process.
### Step By Step
- Spin up a new NGINX container: `docker run -d -p 8080:80 nginx`
- Visit the webpage to make sure that NGINX have been setup correctly.

Step into a new container by executing a **bash shell** inside the container:
```bash
$ docker exec -it CONTAINERNAME bash
```
>[!note] Note that the **CONTAINERNAME** is the name of the **NGINX** container you just started.

Inside, we want to edit the `index.html` page, with a **CLI** text editor called [nano](https://www.nano-editor.org/). Because containers only have the **bare minimum** installed, we need to first install **nano**, and then use it:

>[!note] From the [Docker Hub description](https://hub.docker.com/_/nginx) we know that the standard place for **HTML** pages NGINX serves is in ``/usr/share/nginx/html``

The steps are as follows:
1. Install nano on the container: `apt-get update && apt-get install -y nano`
2. Edit the index html page: `nano /usr/share/nginx/html/index.html`
3. Save and exit nano by pressing: `CTRL + O` and `enter` to save and `CTRL + X` to exit Nano
Then revisit the page to check that your edition is in effect.

### Summary
* Started a new process with the `exec` command in order to look around in a container, or to edit something. 
* Terminating any of the the processes created with `docker exec` will not make the container stop.
## 06 - Docker volumes (Linux)
**Containers should be ephemeral.**
* The whole idea is that you can **start**, **stop** and **delete** the containers without losing data.

**But how can we do that for persistent workloads like databases?**
* We need a way to store data outside of the containers.
* You have two different ways of mounting data from your container `bind mounts` and `volumes`.
### Bind Mount
Bind Mount is the simpler one to understand. It takes a host path, for example `/data`, and mounts it inside your container i.e. `/opt/app/data`.

**The good thing**: 
* Bind mounts are easy and allow you to connect directly to the host filesystem. 
**The downside**:
* You need to specify the host path at runtime, and path to the mount may vary from host to host, which can be confusing when you want to run your containers on different hosts.

With **bind mount** you will also need to deal with **backup, migration etc**. in an tool **outside the Docker ecosystem**. As an example let's look at the nginx container:

The server itself is of little use, if it cannot access our web content on the host. That's why we need to create a mapping between the host system, and the container with the `-v` flag.
```bash
$ docker run --name my-nginx -v /some/content:/usr/share/nginx/html:ro -d nginx
```
Here the `:ro` attribute makes the **host volume read only**, so the container doesn't edit files on the host.
### Volumes
This is where you can use a `named` or `unnamed` volume to store the external data. The data will still be stored locally unless you have configured a **storage driver** for your system.

**Volumes are entities inside Docker**, and can be created in ``3`` different ways:
1. By **explicitly creating it** with the:
	* `docker volume create <volume_name>` command.
2. By **creating a named volume at container creation time** with:
	* `docker container run -d -v DataVolume:/opt/app/data nginx`.
3. By **creating an anonymous volume at container creation time** with:
	* `docker container run -d -v /opt/app/data nginx.`

In the next section, we will try all of them.
### Practical Step By Step
#### Bind Mount
Try to do the following:
1. `$ git clone https://github.com/eficode-academy/docker-katas.git`
2. Navigate to `/docker-katas/labs/volumes` to get the `index.html` file.
3. Change the `/some/content` from earlier, to the right path. Remember, it must be an absolute path (starting from `/`).
4. Now try to run the container with the `labs/volumes` directory bind mounted.
5. Remember to forward the port to 8080 with `-p 8080:80`

This is how my command would look like:
```bash
$ docker run --name nginx-bind -v /home/myuser/docker-katas/labs/volumes/:/usr/share/nginx/html:ro -p 8080:80 -d nginx

c69186961258017b0c80b3e0c1c7cf13d9926bfc4dda0902f51ebddcb50b1ffd
```

Check that it is running by navigating to the **hostname** or **IP** with your browser, and on port **8080**:
![[Pasted image 20241231195015.png]]
Stop the container with `docker stop <container_name>`.
#### Volumes
First off, lets try to make a data volume called `data`:
```bash
$ docker volume create data

#Docker creates the volume and outputs the name of the volume created.
Driver     Volume Name
local      data
```

Now, if we run `docker volume ls` we can see a list of all the volumes created and their driver:
```bash
$ docker volume ls

Driver      Volume Name
local       data
```
Unlike the **bind mount**, you **do not specify** where the data is stored on the host.

In the ``volume API``, like for almost all other of **Docker’s APIs**, there is an `inspect` command giving you low level details.
* Run `docker volume inspect data` to see where the data is stored on the host
```bash
$ docker volume inspect data

[
    {
        "CreatedAt": "2024-12-31T17:54:03Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/data/_data",
        "Name": "data",
        "Options": null,
        "Scope": "local"
    }
]
```
You can see that the `data` volumes is mounted at `/var/lib/docker/volumes/data/_data` on the host. (for more info **refer** to Dockers own example: [example](https://docs.docker.com/engine/storage/volumes/#use-a-volume-driver))

You can now use this **data** volume in **all containers**. Try to mount it to an **nginx** server with:
```bash
$ docker run -d nginx --rm --name nginx-s -p 8080:80 -v data:/usr/share/nginx/html
```
Here: 
- **`docker run`**: Runs a new container.
- **`--rm`**: Automatically removes the container when it stops.
- **`--name www`**: Name the container "nginx-s" for easier reference.
- **`-d`**: Run container in the background.
- **`-p 8080:80`**: Maps port **8080** on the host machine to port **80** in the container. This means you can access the container's web server by navigating to `http://localhost:8080` on your host.
- **`-v data:/usr/share/nginx/html`**: Mounts a volume named `data` from the host to `/usr/share/nginx/html` inside the container. 
- **`nginx`**: Specifies the Docker image to use.
>[!important] **Note:** If the volume refer to is **empty** and we provide the path to a directory that contains data in the base image, that data will be copied into the volume.

Try now to look at the data stored in `/var/lib/docker/volumes/data/_data` on the host:
```bash
ls /var/lib/docker/volumes/data/_data

50x.html  index.html
```
Those two files comes from the **nginx image** and is the standard files the webserver has.
#### Attaching multiple containers to a volume
Multiple containers can attach to the same **volume** with **data**.

Docker doesn't handle any **file locking**, so applications must account for the file locking themselves.

Let's try to go in and make a new html page for nginx to serve. We do this by making a new **ubuntu container** that has the `data` volume attached to `/tmp`, and thereafter **create a new html file** with the `echo` command.

Start the container:
```bash
$ docker run -it -v data:/tmp ubuntu bash
```

In the container run:
```bash
$ echo "<html><h1>hello world</h1></html>" > /tmp/hello.html
```

Then check that the file was created by running in **another container**:
```bash
ls /tmp

hello.html 50x.html index.html
```

#### Clean-up
Exit out of your **ubuntu server** and execute a `docker stop nginx-s` to stop the **nginx** container.

Run a `docker ps` to make sure that no other containers are running:
```bash
$ docker ps

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
The data volume is still present, and will be there until you remove it with a
`docker volume rm data`, or 
make a general clean-up of all the unused volumes by running: `docker volume prune`.
### Tips and tricks
As you have seen, the `-v` flag can both **create** a **bind mount** or **name a volume** depending on the syntax. If the first argument begins with a ``/`` or ``~/`` you're creating a **bind mount**. Remove that, and you're naming the volume. For example:
- `-v /path:/path/in/container` mounts the host directory, `/path` at the `/path/in/container`
- `-v path:/path/in/container` creates a volume named path with no relationship to the host.
#### Sharing data
If you want to share volumes or bind mount between two containers, then use the:
* `--volumes-from` 
option **for the second container**. The parameter maps the mapped volumes from the source container to the container being launched.
### More advanced docker commands
Before you go on, use the [Docker command line interface](https://docs.docker.com/engine/reference/commandline/cli/) documentation to try a few more commands:
- While your **detached** (``-d``) container is running, use the `docker ps` command to see what silly name **Docker** gave your container. **This is one command you're going to use often!**
- While your **detached** (`-d`) container is still running, look at its logs. Try following its logs and refreshing your browser.
- Stop your **detached** (`-d`) container, and confirm that it is stopped with the `ps` command.
- Start it again, wait 10 seconds for it to fire up, and stop it again.
- Then delete that container from your system.
### Summary
Now you have tried to **bind volumes** to a container to connect the host to the container.