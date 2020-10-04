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

### Other useful commands:

`docker container ls -a`
This shows all containers running or stopped. Another way to do it is the shorter alias command: docker ps -a

`docker container top <containerName>`
process list in one container

`docker container inspect <containerName>` 
details of one contaner config

`docker container stats`
performance stats(memory etc) for all containers

### Getting a shell inside containers

`docker container run -it` start new container interactively (no ssh needed)

example:

`docker container run -it --name proxy nginx bash`

or

A full ubuntu distribution:
`docker container run -it --name ubuntu ubuntu`

`docker container exec -it <containerId> <command>` run additional command in existing container running

### Private and Public  comms in containers

`docker container run -p 80:80` Expose ports to the physical network.

`docker container port <container>`

> Concepts of Docker Networking<br>
Defaults:Each container connected to a private virtual network "bridge". Each virtual network routes through NAT firewall on host IP. All container on a virtual network can talk to each onther without -p. Best practice is to create a new virtual network for each app:<br>
1. network "my_web_app" for mysql and apache containers.
2. network "my_api" for mongo and nodejs containers.
> <mark style="background-color:green">Containers can be attacked to more than one virual network or none. Skip virual networkds an duse host IP(--net=host)</mark>

> the default bridge network driver allow containers to communicate with each other when running on the same docker host.


`docker container inspect --format '{{ .NetworkSetting.IPAddress }}' <containerName>` Find out the IP of the network the container uses(not the same as the host?). 
> In mac find the ip address of the host by:<br>
`ifconfig en0`

### Docker Networks CLI Management

`docker network ls`: show networks

`docker network inspect <networkName>`: inspect a network

`docker network create <networkName> --driver`: create a network.Default driver is "bridge"

`docker network connect <networkID>`: attach a network to container

`docker network disconnet <networkID>`: detacha network form container

> Security issues: Create your apps so frontend/backend sit on the same Docker network

### Docker Networks: DNS

1. Do not depend on IPs as inside containers are dynamic. Better use DNS.
2. Use `--link` to anable DNS on default bridge network

> Forget IPs: Static IP's and using IP's for talking to containers is an anti-pattern.

Solution is DNS naming. Docker uses by default the container name as  Host name to communicate between containers. **You can also use aliases**

`docker container exec -it <containerName1> ping <containerName2>` : check connection between two container by pinging.

> Bridge network does not have the DNS server build in by default. use `--link`

`docker container run --rm it ubuntu:14:04 bash`: the --rm removes container when the container stops running (`exit`)

### DNS Round Robin test assignment

Use same name with different IP addresses behind the name. In Docker we can have multiple containers ona a created netwark that respond to the same DNS address(aliases). 

Option for docker run: `--network-alias search` when creating them, so thay are not only "called" by their container name

> Round robin technick is a poor load balancer

Example:

- `docker network create dude`: create network named "dude"
-  `docker container run -d --net dude --network-alias search elastiscearch:2`: create container for "elastiscearch:2" and run it in the background. Attach it to network "dude" and assign an alias with dns name "search"
- run the previous two time to create 2 containers with random container names.
- `docker container run --rm --net dude alpine nslookup search`: create and run container "alpine" attached to network "dude" and use the command `nslookup` to query the DNS to obtain the name or IP address. Then remove container.

> `nslookup` is a network administration command-line tool available in many computer operating systems for querying the Domain Name System (DNS) to obtain domain name or IP address mapping, or other DNS records. The name "nslookup" means "name server lookup"

- `docker container run --rm  --net dude centos curl -s search:9200`: same but for Centos

## Container Images


### What is an image

App binaries and dependencies plus metadata about the image data and how to run the image.

It is not: a comple OS. no kernel, kernel modules (e.g. drivers). The host privides the kernel.

### Docker Hub
Find images:

https://hub.docker.com/search?q=&type=image

https://github.com/docker-library/official-images/tree/master/library

### Image Layers

- Image layers
- union file system
- `history` and `inspect` commands.
- Copy on write

`docker image history <image>`: shows layers of changes made in image.

Every image starts with a blank layers called **scratch**

Reuse same layers(unique SHA), if needed!For example to create another image. This saves time and space ny only stored once on a host.

A container is just a single read/write layer on top of image.
In the container layer, the **copy-on-write** process means that if for example a container runs and attempts to change a file in the image, the file is actually does not change in the image, but a copy is created to the container layer.

`docker image inspect <image>`: returns JSON metadata about the image.