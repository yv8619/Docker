# Docker

- There are chances where Physical servers resources are under utilized. Resources mean CPU, RAM etc.
- To solve this hypervisor concept was introduced which creates virtual servers on top of existing physical server. It does the logical division of existing machine and creates multiple servers.
- Each logical division will have its own OS on which we can run our application.
- Similarly aws have its own physical servers in the data centers which have xen-hypervisors deployed on which we create multiple ec2 instances.
- Again VMs may be under utilized , specially if there are tons of VMs then reseouce utilization will be important.
- To solve this, container concept was introduced. These containers can be built on top of VMs or Physical server itself.
- A monitoring and logging stack, such as CloudWatch, Splunk, Grafana and Prometheus.
- Secrets Management tooling, such as Vault, AWS Secrets Manager	

## why VM is heavy and container is light weight ?
a VM is heavy cos it contains entire OS in isolation where as container shares the resources from the base machine. 
container is a package that contains application, libraries and required dependencies to run the application which is also called base image. 

hardware --> hypervisor --> VM1 ( OS,Lib, App), VM2 ( OS,Lib, App)    ====> separate OS for each VM

hardware --> OS --> Docker --> Container1 (Lib, App), Container2 ( Lib, App)  ====> OS shared by all containers.

hybrid strategy :

harware --> hypervisor --> VM1 ( OS, Docker, (Container1 (Lib, App)), VM2 ( OS, Docker, (Container1 (Lib, App))

======================================================================================================================================

## what is a docker ?
Docker is a platform which implements the containerization concept. 

## what is a docker image ?
A Docker image is a lightweight, standalone, and portable package that contains all the necessary components, including code, runtime, libraries, and dependencies, to run a software application in a containerized environment. Images are created from a set of instructions in a Dockerfile and can be shared and distributed via Docker registries. once a image is built, it can be run on any host having
docker present.  docker file ---> image

## when a image is run, why it is called a container ?
When a Docker image is run, it becomes an active and executable instance of that image, and it is referred to as a "container." A container is essentially a running process that encapsulates the application and its dependencies, isolated from the host system and other containers. Containers provide process isolation. Each container runs as an isolated process on the host system, ensuring that the application and its dependencies do not interfere with other containers or the host environment.Containers encapsulate the application and its dependencies, making them highly portable. They can run consistently across different environments, such as development, testing, and production.	docker file ---> image ---> container

write docker file , submit to docker engine using docker command (docker build) --> will convert to docker image(docker run) --> will convert to container.
Docker engine is a single point of failure,  if engine goes down, all container will stop.Terrible. 


## explain docker architecture ?

- docker client (cli) : user interact with Docker through the Docker client (cli).The client sends commands to the Docker daemon aka docker engine, telling it what to do. 
- docker daemon : Heart of the docker, the "boss" that takes care of everything related to containers.
- docker images : A file which includes everything needed to run an application: code, runtime, libraries, and system tools.Docker uses images as templates to create containers. 
- docker container : Containers are running instances of Docker images.
- docker registry : Docker images can be stored and shared through a Docker Registry. 

uer --> cli --> docker engine

The Docker daemon, also known as the Docker Engine, is a persistent background process that manages Docker containers, images, volumes, networks, and other Docker-related objects on a Docker host. 
It is responsible for handling container lifecycle operations, such as creating, starting, stopping, and removing containers, as well as managing the resources required for container execution.

Containers are Houses: Once you have your blueprint (image), you can create multiple houses (containers) based on that design.


## - rivals of docker ?
Buildah, podman, kubernetes, rocket.

Installaton - 
sudo install docker.io -y
sudo systemctl status docker
docker run hello-world 

a non-root cannot talk to docker engine directly. so we need to add ubuntu user to docker group. Users who need to use Docker can be added to the Docker group. This grants them permission to access the Docker socket and run Docker commands without needing root privileges.
sudo usermod -aG docker ubuntu && newgrp docker
( adds the user ubuntu to the docker group, granting them permission to interact with the Docker daemon, and then switches the user's primary group to docker to immediately activate the changes. )

====================================================================================================================================================
Multi stage docker builds :
Distroless images : a regular Linux distribution has lots of tools and things that applications might not need. It means you only have what's absolutely necessary for your application to run.
By having only what's necessary, Distroless makes things more secure. So biggest advantage of using distroless image is security.

The first stage where the application is built.. the first stage builds the Go application, and the second stage uses a Distroless base image, copying only the compiled binary from the first stage.
This results in a smaller final image that contains only the necessary runtime components for running the application.

So multiple stage buids and distroless images not only enhances security but also make sure that containers run in isolation.

====================================================================================================================================================

Docker volume and Bind Mounts

Container problems:
1) container logs details of user who accessed application on daily basis. If container goes down, the log file gets deleted. 
2) If there are 2 containers FE and BE. BE container writes data to a file or DB which FE reads. If BE container goes down, then FE container will not have data to populate.
3) container can only share resources like cpu, ram but it cannot read any directory of host system. lets say if a container has to read a file present in host system.

solution - 
Bind mounts - allows to bind a directory present on host to container. So even if container goes down, next time we can bind the same directory again present on host. Its like attaching folder present
locally to the container . This folder stores all the logs n data etc.
volumes -  a volume is a way to store and share data between the Docker container and the host system or between multiple containers. It provides a persistent storage solution, allowing data to exist 
outside the container's filesystem. Volumes are managed by Docker and are often stored within Docker-managed directories on the host system (e.g., in /var/lib/docker/volumes).
Volumes can be created and managed using Docker commands (docker volume create, docker volume rm, etc.), and they can be shared between multiple containers.
Unlike mount, a volume can be created on host, or any ec2 instance, NFS, S3 etc.

docker volume ls
docker volume create mystorage
docker volume inspect mystorage
docker volume rm mystorage
docker run -d --mount source=mystorage,target=/app myimage  ( mounting volume to container )

====================================================================================================================================================

what is docker ?
how is container different from VMs ?
what is docker life cycle ?
what are different docker components ?
what is diff btw docker copy and docker add ?
what is diff btw CMD and entry point in docker ?
what are the networking types in docker and what is the default ?
can you explain how to isolate networking btw containers ?
what is multi stage build in docker ?
what are distroless images in docker ?
real time challenges with docker ?
what steps would you take to secure containers ?

====================================================================================================================================================

Installation -

sudo apt update
sudo apt install docker.io -y
docker run hello-world:latest  ( docker run image_name)

 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

docker - check first if docker is running
docker pull image_name
docker images ( list all images )
docker ps -a ( list all containers )
docker run nginx ( start a container )
docker run nginx:4.0  ( 4.0 is called tag )
docker stop container_id  ( stop a container )
docker rm container_id  ( delete a container, first 3 digits of containerid can be used )

docker rmi image_id  ( make sure no container is running for that image before deleting )
docker pull hello-world  ( pull only image from hub, and dont run )

docker run -it nginx bash ( start an interactive Bash shell within the container. )
docker run -d centos sleep 20  ( docker will exit container after 20 secs )

docker exec containerid cat /etc/*release*  ( to print content of file of a running container )

docker run --name webapp nginx-1.14  ( run a container and name it webapp )

====================================================================================================================================================

Docker Port mapping - also known as port forwarding, is a mechanism that allows you to map network ports on the host machine to ports on a running Docker container. This feature enables external 
services or users to access specific services running within the container.

laptop port mapped to container port.

docker run -p 8080:80 nginx

====================================================================================================================================================

CREATING IMAGE - using docker file

From Ubuntu ( base image )

RUN	apt-get update && apt-get -y install python
RUN pip install flask flask-mysql
COPY . /opt/source-code
ENTRYPOINT 

after creating image, we build the image
docker build . -t my_first_image

docker search python  ( will list images of all size )


Run a container named blue-app using image kodekloud/simple-webapp and set the environment variable APP_COLOR to blue. Make the application available on port 38282 on the host. The application listens on port 8080.
docker run -d --name blue-app -p 38282:8080 -e APP_COLOR=blue kodekloud/simple-webapp

docker run --name mysql-db -e MYSQL_ROOT_PASSWORD=db_pass123 mysql

====================================================================================================================================================

CMD vs ENTRYPOINT

In Docker, both CMD and ENTRYPOINT are instructions that define the command to be executed when a container is run.
The CMD instruction specifies the default command and/or parameters to be executed when the container starts.
You can have only one CMD instruction in a Dockerfile.
If the user provides a command when running the container, it will override the CMD instruction.

2 ways to give cmd :
exe form ( array of strings ) - CMD ["executable", "param1", "param2"]
shell form ( as string) - CMD command param1 param2

ENTRYPOINT Instruction:
The ENTRYPOINT instruction sets the primary command and parameters that are always executed, regardless of any arguments passed at runtime.
If the user provides a command when running the container, it is appended to the ENTRYPOINT command.

====================================================================================================================================================

## Docker Registry

images are storied in docker hub (docker.io). 
In house application images are storied in private registry. we need to login to priviate registry in order to pull a image.

docker login private-registry.io

Run a registry server with name equals to my-registry using registry:2 image with host port set to 5000, and restart policy set to always.
Note: Registry server is exposed on port 5000 in the image.
Here we are hosting our own registry using the open source Docker Registry

docker run -d -p 5000:5000 --restart=always --name my-registry registry:2

Docker private registry in itself is an image.
docker run -d -p 5000:5000 --name registry registry:2  ( building a container from registry:2 image )

tag your image to private repo :
docker image tag myImage localhost:5000/myImage

push your image to private repo :
docker push localhost:5000/myImage

====================================================================================================================================================

## Docker networking - 

docker networking offers solution like
- one container can talk to another container
- complete isolation btw 2 containers
There should be a networking way where one Ip address can talk to another ip address.

Bridge - default network.    rs connected to the bridge network can communicate with each other using IP addresses assigned by Docker's built-in DNS resolver.
Docker automatically assigns IP addresses to containers on the bridge network and manages connectivity between them.
 

None Network:
Creates a container with no network interfaces attached.
Useful for containers that don't need network connectivity or that have custom network configurations managed externally.
Containers on the none network cannot communicate with other containers or external resources unless additional networking is configured manually.

docker inspect containerid - under network section we could see the IP assigned.

find all networks:
docker network ls
docker network inspect bridge  - subnet configured on bridge network

Run a container named alpine-2 using the alpine image and attach it to the none network.
docker run --name alpine-2 --network=none alpine

Create a new network named wp-mysql-network using the bridge driver. Allocate subnet 182.18.0.1/24. Configure Gateway 182.18.0.1
docker network create --driver bridge --subnet 182.18.0.1/24 --gateway 182.18.0.1 wp-mysql-network


Deploy a mysql database using the mysql:5.6 image and name it mysql-db. Attach it to the newly created network wp-mysql-network
Set the database password to use db_pass123. The environment variable to set is MYSQL_ROOT_PASSWORD.

docker run -d -e MYSQL_ROOT_PASSWORD=db_pass123 --name mysql-db --network wp-mysql-network mysql:5.6


====================================================================================================================================================

Docker storage - Docker stores data on local host at  /var/lib/docker . This have folder like aufs, container, image, volumes


## How is docker used in kubernetes ?

Docker is used in Kubernetes as the container runtime, responsible for managing the lifecycle of containers within the cluster. Here's how Docker fits into the Kubernetes architecture and how it's used:

Kubernetes relies on a container runtime to create, run, and manage containers. Docker is one of the most popular container runtimes and was the original choice for Kubernetes.
Pods: In Kubernetes, the smallest deployable unit is a pod. A pod can contain one or more containers that share the same network namespace, storage, and other resources. Docker is used to create and run these containers within the pod. 
Docker images are used to package applications and their dependencies into a single, portable unit. Kubernetes pulls Docker images from container registries (such as Docker Hub) and deploys them as containers within pods.Each node in a Kubernetes cluster runs a component called the kubelet, which is responsible for managing pods and containers on that node. The kubelet interacts with Docker (or another container runtime) to start, stop, and monitor containers. 
When Kubernetes schedules a pod to a node, the kubelet instructs Docker to pull the required Docker images and create containers based on those images. The kubelet then monitors the containers' health and restarts them if they fail or become unresponsive.Kubernetes uses Docker's features for resource isolation and management, such as namespaces, cgroups, and container networking. This allows Kubernetes to provide strong isolation between containers and efficient resource utilization across the cluster.
Kubernetes interacts with Docker through the Container Runtime Interface (CRI), a standardized interface for container runtimes. This means that Kubernetes can work with any container runtime that implements the CRI, providing flexibility and interoperability.

kubernetes --> docker api/docker CRI  --> docker hub/jrog --> pull image --> create container..n  --> pod ---> node (kubelet) --> kubelet monitor container health

=================================================================================================================================================

## how many images are available ?
docker images

## how to check size of image ?
docker inspect ubuntu

## Build a docker image using the Dockerfile and name it webapp-color. No tag to be specified.
docker build -t webapp-color .

## how to remove un-used docker resources which are claiming disk spaces ?
using prune command. But it should be carefully used as it will delete the resource along with its data permanently. So you may lose the data.
docker image prune
docker container prune
docker volume prune
docker network prune

## how many containers can you create in docker ?
There is no explicit limit but It will depend on the hardware contraints like no of cpu available and size of the application. lighter the application, lesser it will consume cpu.

## how to limit the memory and no of cpu which can be used for any given container ?
we can set the cpu limit using --cpu option and memory limit with --memory option. 
docker run --cpu 2 --memory 1g container_name
docker exec my_container ls /app

## what is docker file ?
dockerfile is a text file that contain commands to build an image. It can specify base image, files to add to the container, commands to run during the build process and commands to run
when container starts.

## what is the purpose of CMD instruction in docker file ?
The CMD instruction specifices the default command to be run when container starts. It defnies the primary functonality of container such as running application or running a script.

## how would you scale docker containers ?
Using docker swarm or kubernetes.

## how to debug issues in failing container ?
There are several ways
1) logging - docker logs
2) shell command
 doker exec -it container_name bash ( Execute a shell inside a running container )
docker exec my_container ls /app  ( Run a command inside a container )
3) image inspection - docker image inspect


## how do you update container w.o losing data ?
create backup of data.
stop container, docker stop
pull latest version of docker image, docker pull
start a new container

## how do you secure docker containers ?
using trusted base image from reputable sources
regularly updating underlygin host system with security patches
scanning containers for vulnerabilities.
applying least privelge principle.
loggin for securty analysis.

## Did you use docker with ci/cd tool ?
Yes, with Jenkins. Jenkins would trigger a job where it builds new docker image using the docker file . This image is pushed to jfrog artifactory where it is pulled and deployed into production
env using kubernetes.

## how is docker used within aws ?
we utilized docker within aws ecs. we also used aws fargate for serverless compute for our containers.

## how do you use docker with kubernetes ?
kubernetes uses docker api to interact with docker and perform container related tasks like pulling images, creating containers and managing their lifecycle. docker images are typically
stored in docker registry or other repository like jfrog accessible to kubernetes cluster.


## how did you use terraform to automate the deployment of docker containers ?
Terraform provides a Docker provider that allows you to interact with Docker resources, such as containers, images, networks, and volumes. we define Docker resources in your Terraform configuration using resource blocks, specifying attributes such as image, name, port mappings, environment variables, and volumes. After defining your Terraform configuration, you run terraform plan and terraform apply. 

provider "docker" {
  host = "tcp://localhost:2375"
}

resource "docker_container" "my_container" {
  name  = "my-container"
  image = "nginx:latest"
  ports {
    internal = 80
    external = 8080
  }
}


## how do you implement canary deployment with docker and kubernetes ?
set up a kubernetes cluster and deploy your application using docker container. create a new deployment for the updated version. configure the canary deployment to receive small % of traffic
and gradually increare the traffic based on feedback n stability. 























































