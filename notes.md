# General Notes 

### Scope
- Docker Install
- Container Basics
- Image Basics
- Docker Networking
- Docker Volumes
- Docker Compose
- Orchestration
- Docker Swarm
- Kumbernetes
- Swarm vs K8s
- Docker Security

### Now is  the age of Host to Container (Serverless). Docker is all about speed!

All examples and assignment files are in a single code repository on GitHub. Each folder represents a single lecture video (but not all lectures have a folder, as not all need code). You should clone this repo into your user profile somewhere on your host. (c:/Users/username/  on Windows and /Users/username  on Mac). Having inside your user profile will ensure Docker works correctly on Mac and Windows. The location doesn't matter for Linux hosts. git clone https://github.com/BretFisher/udemy-docker-mastery.git 


## Install

Open source Docker CE(Commutity) free
store.docker.com

Editions: `Direct`, `Mac/Win`, `Cloud`

- There are Cloud editions for AWS/Azure/Google.


`Stable` vs `Edge`
- Edge is a beta version once per month
- Stable once per 4 months

### Installing on Windows 10 (Pro or Enterprise)

This is the best experience on Windows, but due to OS feature requirements, it only works on the Pro and Enterprise editions of Windows 10 (with latest update rollups). You need to install "Docker for Windows" from the Docker Store.

With this Edition I recommend using PowerShell for the best CLI experience. See more info in the next few Lectures

Lucky to have Enterprice beacause the Docker for Windows uses Hyper-V with a tiny Linux VM for Linux Containers.

Two types of containers: `Linux` and `Windows`. But the default is `Linux`. 

Docker's version is now YY.MM  based, using the month of its expected release, and the first one will be 17.03.0 , which is "the first point release" of the 17.03  release. If any bug/security fixes are needed to that release, the first update will be 17.03.1  etc.

Docker Desktop for Windows Terminals
On Windows you'll likely want to use PowerShell terminal (PowerShell.exe) rather than Command Prompt (cmd.exe), but if you don't like the default GUI window that Microsoft provides, you might want to check out cmder, which can run PowerShell inside a fancy tab-based GUI with lots of features. You can also use Bash shells but you're on your own for setting those up.


## Creating Containers

Commands:
- `docker` : see all the commands
- `docker version`: version info
- `docker info` : more info including the number of images etc.
- new Format: `docker <command> <sub-command>`
- old Format: `docker <command>`

- Image vs Container
`Image`: binaries
`Container`: running instance of the image
Many container based on the same image

Nginx web server as image for this lecture.
Docker's default image registry is called Docer Hub (hub.docker.com)

`docker container run --publish 80:80 nginx` 
1. Searched first locally for an image called nginx and then if does not find it, it downloads it from the Docker Hub.
2. Started it as a new container from that image
3. Opened prot 80 on the host IP
4. Routes that traffic to the container IP, port 80
(example 8080:80)

`docker container run --publish 80:80 --detach nginx`
1. --detach will run in the background.
2. returns the unique container ID

`docker container ls`

`docker container stop <first few digits of container ID>`

`docker container -a`
1. Displays all containers created. Name also needs to be unique. Generates random name if not provided.

run/stop/remove containers
check container logs and processes

- Manage Multiple Containers

Example: nginx(80:80), mysql(3306:3306), httpd(8080:80)
When running mysql, use the --env to pass MYSQL_RANGOM_ROOT_PASSWORD=yes (see docker container logs)

Commands:

`docker container run -d -p 3306:3306 --name db -e MYSQL_RANGOM_ROOT_PASSWORD=yes mysql`

`docker container logs`

`docker container run -d -p 8080:80 --name webserver httpd `

`docker container run -d --name proxy -p 80:80 nginx`

`curl localhost:8080`

`docker container stop <containerID> <containerID> <containerID>`

`docker container rm <containerID> <containerID> <containerID>`