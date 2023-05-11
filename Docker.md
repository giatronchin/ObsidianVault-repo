#docker #container #linux 

Run a container
```bash
docker run <container-img>
```

Check all container runned (and also stopped)
```bash
docker ps -a 
```

Create a volume to attach to containers
```bash
docker volume create <volume-name>
```

Stop and remove container running/stopped
```bash
docker rm -f <container-id>
```

Create image from an existing container (grab CONTAINER_ID first)
```bash
docker commit CONTAINER_ID image_name:version_tag
```