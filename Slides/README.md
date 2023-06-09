# Walkthrough 
## Disclaimer
This contains (or will contain) slides, that is is - no viruses as far as I know.
## Outline
- [Walkthrough](#walkthrough)
  - [Disclaimer](#disclaimer)
  - [Outline](#outline)
  - [Basic Terminology](#basic-terminology)
  - [Basic Linux Commands](#basic-linux-commands)
  - [Difference between Containers and VMs](#difference-between-containers-and-vms)
  - [What is an Image](#what-is-an-image)
  - [What is an Container](#what-is-an-container)
  - [What are Volumes](#what-are-volumes)
  - [What are Docker Networks](#what-are-docker-networks)
  - [Dockerfiles](#dockerfiles)
    - [Docker from Scratch](#docker-from-scratch)
    - [Dockerfile from Base Image](#dockerfile-from-base-image)
    - [Building the container](#building-the-container)
    - [Docker Hub](#docker-hub)
  - [Docker CLI Commands](#docker-cli-commands)
  - [Docker through Portainer](#docker-through-portainer)
## Basic Terminology 
 **Ports**: These are structures that the system uses to demux connections to different services - 1 - 1023 are for well known services and require privileged (root) privileges to bind to 

 **Dynamic Linking**: This is a process of liking library's where the library links or "cooks" calls to external librarys once a program is loaded, as we do not know where they will be loaded in memory at compile time. This allows multiple programs to share one library (loaded in memory) reducing memory usage - and a programs binary size.

 **Static Linking**: This is a process of linking the library's at compile time, they are added to the programs binary (code) and linking is locally relative. [Probably reword]

 **Namespaces**: This is a kernel structure which allows us to isolate or partition various "things" or resources a process may use such as the network stack or process namespace. This allows us to isolate processes or their view of the system from one another. 

 **CGroups**: This is also a kernel structure that allows us to limit the consumption or possible allocation of computing resources to a process or set of processes (If it is applied to a group of them/namespace)

 **Capabilities:**: This is what we use to grant privlages to a given container, to do actions on/with host system resources they would otherwise be unable to do. Such as allowing them to use Raw network sockets.

 **System Calls**: This is a mechanism that is used to interact with the host kernel, and by extension the hardware of the system. All programs will make system calls, and we can examin this using the **strace** program on linux. Be warned there will be alot of output, and most of it makes little sense. For example you will likely see the **brk** system call, what does this mean? Well this comes from the time the end/top of the heap was called the break and is used when allocating more space to the heap. **I may be spreading misinformation here** 

 **Linux Filesystem**: This is the normal filesystem we interact with, consisting of links to INodes. All of the things in a linux system are represented as files, this includes directories (folders) they are just a special type of file.

 **Proc Filesystem**: This is a "fake" filesystem, in that it is not stored on the disk and exists entirely in the volatile memory of the system, we can examine the information contained to learn more about the processes running on the system. Even accessing their file descriptors to send information to them (Think pipes)

 **Docker Socket**: This is a special socket that is used to communicate with the Docker Engine. You should not expose this to the network or especially the internet unless you know what you are doing.

 **Rootless Docker**: This is a configuration that can be made so the docker containers that the engine creates are ran as a non-root user with a UID > 0, other containerization implementations such as podman do this automatically. Having containers run as privileged processes (the root user) is dangerous, as if they are able to escape the container then they have a root shell.
   * **UID**: The ID associated with a USER and the processes they generate
   * **root user**: This is a privileged user with the UID of 0, they can access and control everything on the system.


**Daemon**: Backround processes that often control background processes or configure the system when it is started
    **Examples**: The docker daemon, auditd daemon and the systemd daemon to name a few.

## Basic Linux Commands 
This is so those unfamiliar with linux can follow along. Although you should become more familiar before you attempt to go further with docker, or use it as a good learning experience.


```sh 
# Change current directory to another directory 
$ cd <path/to/dir>

# Push the current path onto the directory stack
$ pushd .

# Goes to the directory on the top of the stack
$ popd

# Get path to the current working directory  
$ pwd

# Moves a file to a new location 
$ mv <file> <path/to/location>

# Compile something to be an executable (ELF - Extended Linking Format) file 
$ gcc <file> -o <output-file-name>
```
## Difference between Containers and VMs
At first glance it may not seem like there are many differences, but there are a number of key (and minute) differences. 

We can see some noticeable characteristics. 
* In the case of a VM, the Hypervisor (manager of Guest OSs) can take the place of the Operating system on the machine, this is a **Type 1** hypervisor, this EXSCI and Proxmox.
  * Containers cannot do this, they rely on the Kernel of the Host OS to execute **systemcalls**
* You will notice that the VMs carry a OS Kernel with them.
  * This means that you can host VMs with operating systems that are different from the host
  * This also means there is a lot of repeated information between VMs with the same OS
  * Additionally you cannot have the container daemon - the thing managing the containers replace the host OS as it requires a OS kernel to execute systemcalls (code)
* You will also notice that the containers have only what they need, they are a way to package up the minimal amount of resources to run an application
## What is an Image
Images are a **blueprint** as to what the base of the conatiner should be.

Images are imitable, in the sense that they are only changed when a new one is build and has the same tag. Changes container make during their runtime are made in the scratch space memory provided to running containers. These changes will not be reflected in the base image, or any other currently running containers (unless the changes are made to files located in volumes).

We define images using [Dockerfiles](#dockerfiles).

## What is an Container
Containers are running instances of images. They are really just a set of **isolated processes** sharing a set of isolated resources, this being the network, filesystem, IPC, ect. They utilize the **host** kernel and hardware for executing instructions - so the types of software we run on them must be able to run on the host.

Containers are created using CGroups and Namespaces, so if you look at the different types of namespaces you can get an idea of the isolation involved; **process**, **network**, **mount (filesystem)**,**inter process communication**, and **CGroup** namespaces to name a few. CGroups are utilized to limit the amount of resources a process or set of processes can utilize at a given time, as containers are just a set of isolated processes running on the host system we can apply CGroups to them! However I will not be going over this...

Containers are not persistent, they are ephemeral in nature. Changes made on a container are not preserved once the container is deleted. We can utilize some additional Docker structures to add a form of persistance to the containers.

Something to note is the Host Operating System **must** support some structure analogous to  
## What are Volumes
Volumes are used to provide persistance to containers, we mount or bind the volume to a specified part of the container's file system. The files in this volume will be stored at a specific location on the host's machine. Multiple containers can be attached to this volume, and changed made will be reflected on all containers.
## What are Docker Networks 
Docker networks are a **virtual network** that may be bridged to the host's adaptor to grant them access to the internet. We can create new networks or use prexisting ones so that we can partition the containers into their own isolated networks to prevent or limit access and traversals. 

The networks are private and not directly accessible.
## Dockerfiles 
These are the files that we can use to define how a new image should be created. This is the assembly of Docker. It has its own "language" which is really just a set of keywords that define what actions will be taken givens some input. This will not be a definitive guide, as one already exists in the [Docker documentation](https://docs.docker.com/engine/reference/builder/#:~:text=A%20Dockerfile%20is%20a%20text,can%20use%20in%20a%20Dockerfile%20).

The main thing to point out for the purposes of this presentation is that **each line** in the Dockerfile represents a layer of actions that is taken, and saved. They define the Image that is produced. We can start from **scratch** or from a pre-existing base image - these base images were originally built from scratch.

When creating Dockerfiles we specify the commands that should be run, the files we copy over from the host machine, and what program should be run to once the image is started.

**NOTE**: All Dockerfiles should be named **Dockerfile**, otherwise we will need to specify this in the build command
### Docker from Scratch
```Dockerfile
# Define Base image
FROM scratch
# Copy the static program over
copy some_static_program /
# Run the static program
CMD["./some_static_program"]
```
We just defined a image that will contain a statically compiled program, the static compilation is important as there are no shared libraries (Dynamic Linking Libraries in Windows terms) in this image. We could go through the trouble of adding them, however in this case it is easier to statically compile a program so it contains all the necessary libraries and functions to run in the barren environment that is a container built from scratch.  
### Dockerfile from Base Image
```Dockerfile
# Specify the base image we are going to use
FROM ubuntu:latest
# Set Environment variable so we can install nginx 
ENV DEBIAN_FRONTEND noninteractive
# Update package list
RUN apt -y update
# Install nginx
RUN apt install nginx -y
# Change to root directory
WORKDIR /
# Copy entrypoint script to container
COPY entrypoint.sh .
# Run Script
ENTRYPOINT ["/bin/bash", "entrypoint.sh"]
```
This is a nginx container built off of a ubuntu container. We can use the programs like **apt** that are normally present in Ubuntu as someone has already configured this image to have them. Something to note is we are using an **entrypoint.sh** script that will contain the command **tail -f /dev/null**. This is a common practice as the entrypoint script will run forever, meaning the container will run untill it is stopped, or something kill the entrypoint script. 

If we had the following:
```Dockerfile
...
ENTRYPOINT ["/bin/bash", "sleep 10"]
```
The container would be killed after 10 seconds, as the command we specify with the **ENTRYPOINT** or last **CMD** directive will have the pid 1, and the container will die once the process ends.

### Building the container
We can use the following command to build and "tag" a container. The tag is a name and version.
```sh
$ docker build -t <name>:<tag>
```
### Docker Hub
If we want to push a container to the docker hub we need to do the following 
1. Build and tag the container
  * docker build -t username/containername:latest
1. If the container is not tagged with your username you can do the following 
  * docker tag \<old_image\> username/container_name
2. Push the cotnainer
  * docker push image_name

Be sure you are logged in 
1. docker login -p \<passwd\> -u \<username\>
  * You can use the --password-stdin flag instead of -p
## Docker CLI Commands
There are quite a few docker commands that we can use through the CLI each with some often large set of flags to influence their behavior. The following will be a few commands that may be useful with a small set of flags.

...
All the flags other than the last one are optional
* docker run: 
  * --rm: Remove once stopped
  * -d: Put container process in the background - do not bind to terminal in/out
  * -p \<host\>:\<container\>: Forward traffic to the host on \<host\> port to the container's \<container\> port.
  * --name \<name\>: Give the container this name, it can be used within docker to resolve the containers IP
  * --hostname \<name\>: Change the container's hostname
  * -v \<path/on/host\>:\<path/on/container\>: Mount the \<path/on/host\> file/dir to the container at \<path/on/container\>
  * --network \<network\>: Attach the container to the specified docker network
  * \<image_name\>: The image that will be turned into a container
* docker stop \<container_name/ID\>: Stop a container
* docker start \<container_name/ID\>: Start a stopped container
* docker rm \<container_name/ID\>: remove a container
  * -f (Force can be added if the container is running)
* docker network:
  * create: Create a new docker network
    * -d \<driver\>: Create a docker network with the specified driver
    * \<network\>: Network name 
  * rm: Remove the specified network (may be able to force with -f)
    * \<network\>
* docker inspect: Show information about the specified object
  * \<Object\>
* docker ps -a: List all containers on a machine running or not
* docker logs \<container_name/ID\>: List all stdout/stderr output
* docker kill: Kill a docker process
  * -s HUP: Send the HUP (Hang Up) signal of 1, this will often signal a process to re-read the configs
  * \<container_name/ID\>

## Docker through Portainer
This is a docker management software that uses a GUI to make management more intuitive and potentially easier. 

Run the following command:
```sh
$ docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

Access the website over
```
https://<some-IP>:9443
```

Create an account and start playing around.