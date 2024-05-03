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
==Note==: `CMD` and `ENTRYPOINT` are the command launched within the container at the building time.

To build the image, use the following command. The tag will represent the container image release. 
```bash
docker build -t our-first-image -f /path/to/fileName #Only if fileName is not Dockerfile
```

**LABEL** are not the same thing as they provide your container with metadata to keep them documented and organized.

To run that image use
```bash
docker run our-first-image
```

## Docker Compose
```bash
docker compose -up
docker compose build
docker compose create <service-name>
docker compose start <service-name>

docker compose stop <service/container-name>
docker compose rm <service/container-name>
docker compose down
```

==**environment variables**== - Accessible inside the running Docker container

==**build-arguments**== - Accessible only at build time
- Specify *build tool version*
- Specify *cloud platform configuration* (e.g  AWS region)

To specify these *parameters* use the following syntax:
```yaml
#docker-compose.yml
---

services:
	storefront:
		build:
			context: . #path to where the 'Dockerfile' is located
			args:
				- <argument-name> ="<argument-value>"
		environment:
			- runtime_env #same env variable defined in the host system
		volume:
			- /path/host:/path/container #if host portion is not specified docker will create it
	database:
		image: "mysql"
```

If the list of environment variables gets too long use a file list (env). Change the `docker-compose` as following:

```yaml
#docker-compose.yml
---

services:
	storefront:
		build:
			context: . #path to where the 'Dockerfile' is located
			args:
				- <argument-name> ="<argument-value>"
		environment:
			- runtime_env #same env variable defined in the host system
		volume:
			- /path/host:/path/container:rw # 'rw' means read and write permission will be allowed
	database:
		image: "mysql"
		env_file:
			./path/env_file
```

```
#env

MYSQL_ROOT_PASSWORD=mUmySRsF3*ec7NXrFEXWegTH
MYSQL_DATABASE=ldcsites_wp
MYSQL_USER=giacomo
MYSQL_PASSWORD=SuperSecret_ChangeMe

```

Volume can also be declared with a **name** instead of *host path*:
```yaml
#docker-compose.yml
---

services:
	storefront:
		build:
			context: . 
			args:
				- <argument-name> ="<argument-value>"
		environment:
			- runtime_env 
		volume:
			- /path/host:/path/container:rw 
			- anything:/var/lib/mysql 
	database:
		image: "mysql"
		env_file:
			./path/env_file

volumes:
	anything: #named volume

```

Data will be persisted between newly created and older container. At the same time data will be free completly when running `docker compose down --volumes` which will automatically delete the volume.

*Every time we launch a container a new volume will be created*

## Container dependencies
When a container depend from another to being able to run correctly.
```yaml
#docker-compose.yml
---

services:
	scheduler:
		build: scheduler/.
		ports:
			- "81:80"
	storefront:
		build:
			context: . 
			args:
				- <argument-name> ="<argument-value>"
		ports:
			- "80:80"
			- "443:443"
		depends_on:
			- database #compose will start and stop container in dependency order (first the database service and then the storefront)
		environment:
			- runtime_env 
		volume:
			- /path/host:/path/container:rw 
			- anything:/var/lib/mysql 
	database:
		image: "mysql"
		env_file:
			./path/env_file

volumes:
	anything: #named volume

```