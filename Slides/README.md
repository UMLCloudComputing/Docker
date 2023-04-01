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
    - [Building on base images](#building-on-base-images)
    - [Docker Hub](#docker-hub)
  - [Docker CLI Commands](#docker-cli-commands)
  - [Docker through Portainer](#docker-through-portainer)
  - [Uses of Docker](#uses-of-docker)
## Basic Terminology 
 **Ports**: These are structures that the system uses to demux connections to different services - 1 - 1023 are for well known services and require privileged (root) privileges to bind to 
 **Dynamic Linking**: This is a process of liking library's where the library links or "cooks" calls to external librarys once a program is loaded, as we do not know where they will be loaded in memory at compile time. This allows multiple programs to share one library (loaded in memory) reducing memory usage - and a programs binary size.
 **Static Linking**: This is a process of linking the library's at compile time, they are added to the programs binary (code) and linking is locally relative. [Probably reword]
 **Namespaces**: This is a kernel structure which allows us to isolate various "things" or parts a process may use such as the network stack or process namespace.
 **CGroups**: This is a 
 **Capabilities:**:
 **System Calls**:
 **Linux Filesystem**:
 **Proc Filesystem**:
 **Docker Socket**:
 **Rootless Docker**:
   * **UID**:
   * **root user**:


**Daemon**:
    **Examples**:

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

## Dockerfiles 
### Docker from Scratch
### Building on base images
### Docker Hub

## Docker CLI Commands

## Docker through Portainer

## Uses of Docker 