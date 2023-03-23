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
  - [What are Networks](#what-are-networks)
  - [Dockerfiles](#dockerfiles)
    - [Docker from Scratch](#docker-from-scratch)
    - [Building on base images](#building-on-base-images)
    - [Docker Hub](#docker-hub)
  - [Docker CLI Commands](#docker-cli-commands)
  - [Docker through Portainer](#docker-through-portainer)
  - [Uses of Docker](#uses-of-docker)
## Basic Terminology 
 **Ports**:
 **Dynamic Linking**:
 **Static Linking**:
 **Namespaces**:
 **CGroups**:
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

## What is an Container

## What are Volumes

## What are Networks 

## Dockerfiles 
### Docker from Scratch
### Building on base images
### Docker Hub

## Docker CLI Commands

## Docker through Portainer

## Uses of Docker 