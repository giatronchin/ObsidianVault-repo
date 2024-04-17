#docker #container #linux 

Create a docker container without running it
```bash
docker create <container-img>
```
Run a docker container created
```bash
docker container start <container-id>
```
To take a look of the live output of a running but detached container
```bash
docker log <container-id>
```
Attach current shell with STDOUT of the running container
```bash
docker container attach <container-id>
```
Run a container (automatically attach shell to container's shell)
```bash
docker run --name <name-specific-container-instance> <container-img>

docker run  -d <container-img> #DETACH SHELL
```
Behind the scene
```bash
docker run <container-img> = 
docker create <container-img>
+ docker container start 
+ docker container attach 
```

==Note==: by default docker will not create or delete container by itself

To execute additional command in the container without login-in
```bash
docker exec <container-id> <command>
```

To interact with the container shell and being able to quit the container
```bash
docker exec --interactive --tty <container-id> <shell> # 'docker exec -it bash' would have the same results 

# '--interactive' if we need to send key-combination to the container shell
# '--tty' to make container shell communicate with our current shell
```

Check all container run and also stopped
```bash
docker ps -a #just ps show only "running" container
```
To kill a container
```bash
docker kill <container-id>
```
To stop a container 
```basg
docker stop <container-id> -t 0 #Stop container without waiting for graceful stop (-t 0)
```
To remove a container and stop it at the same time
```bash
docker rm <container-id> #if we want to spare container that are currently running

docker rm -f <container-id> 
```

To remove and image, first of all make sure that the image is not being used by an existing container
```bash
docker rmi <image-name>
```

To remove and stop a list of containers use this trick
```bash
docker ps -aq | xargs docker rm

docker ps -aq #only return containers ID

xargs docker rm #perform 'docker rm' over a list of initial parameters (above piped by the previous command)
```
## Port Mapping

```bash
docker run -d --name our-web-server -p OUTSIDE_HOST_PORT:INSIDE_CONTAINER_PORT <container-img>
```
## Volumes
Data in container are not persisted by default. There are 2 strategy to deploy `volume-bind` or `mount-bind`.

Create a volume to attach to containers
```bash
docker volume create <volume-name>
```
To attach a specific path host <-> container
```bash
docker run -v /path/to/host:/path/to/container <container-img>
```
==Note==: if we are mounting **files** with `-v` we need to make sure that the file exist on the host filesystem. Wether if we are about to mount a directory and it does not exist on the host-filesystem docker will proceed to create it for us.

## Container Registries
An effective method to push and keep track of version of images. By default on DockerHub.

```bash
docker login #login to DockerHub

docker tag  our-web-server username/our-web-server:<version-number> #rename docker images. To push it to our registry 

docker push username/our-web-server:<version-number>
```

Create image from an existing container (grab CONTAINER_ID first)
```bash
docker commit CONTAINER_ID image_name:version_tag
```

## Dockerfile & Img Building

Example of `Dockerfile` 
```bash
FROM ubuntu #Build from this original img

LABEL maintainer="Gigi TheStone <dev@domain.com>"

USER root #following command will be runned from 'root' within the container

COPY ./entrypoint.bash / #copy file from host to container fs

RUN apt -y update
RUN apt -y install curl bash
RUN chmod 755 /entrypoint.bash

USER nobody #change to login to least priviledged user on the container system

ENTRYPOINT [ "/entrypoint.bash" ] #Could also used CMD but there are some differences
```

To build the image, use the following command. The tag will represent the container image name.
```bash
docker build -t our-first-image -f /path/to/fileName #Only if fileName is not Dockerfile
```

To run that image use
```bash
docker run our-first-image
```

## Docker Utils

```bash
docker stats #return stats about docker environments

docker inspect #return advance info of a container in a JSON format
```