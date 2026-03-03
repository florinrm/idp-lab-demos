## General commands

- pull an image and run a container based on the pulled image: `docker container run hello-world`
- check if Docker works well: `docker ps`
- pull an image: `docker image pull alpine`
- run a command in a container: `docker container run alpine ls -l`
- see all containers (including exited ones): `docker container ls -a` / `docker ps -a`
- see all active containers: `docker container ls` / `docker ps`
- see images: `docker images` / `docker image ls`
- run a container in interactive mode: `docker container run -it alpine`
- delete container: `docker rm <container-id>`
- delete image: `docker rmi <image-id>`
- delete used image: `docker rmi <image-id> --force`
- run a container in background (daemon) mode: `docker container run -d -it alpine` - without `-it` parameter the container won't run
- attach to a container which runs in background: `docker attach <container-id`
- stop running a container: `docker stop <container-id>`
- show info about a container: `docker inspect <container-id>`


## Working with images build using Dockerfile

- building image using a Dockerfile: `docker build -t testapp .` - `-t` parameter is used for naming (in this case `testapp`) the built image
- check details about an image: `docker image inspect <image-id or image-name>`
- start a container based on an image, with port mapping: `docker container run -p 8888:5000 testapp` - 8888 is localhost port and 5000 is port used in container

## Publishing a Docker image to DockerHub
```bash
docker build -t idp-lab1-go-mod . # build an image
docker tag idp-lab1-go-mod florinrm/idp-lab1-go-mod # tag an image - login first
docker push florinrm/idp-lab1-go-mod # publish the image
docker run -p 8888:5000 florinrm/idp-lab1-go-mod # start a container with the published image
```

## Networking
- start containers (with names):
```bash
docker container run --name c1 -d -it alpine
docker container run --name c2 -d -it alpine
```

- disconnect container from a network:
```bash
docker network disconnect bridge c1
docker network disconnect bridge c2
```

- try to ping a container from an another one
```bash
docker exec -it c1 ash
ping c2
```

- create network: `docker network create -d bridge c1-c2-bridge`
- check networks: `docker network ls`
- add containers to networks:
```bash
# if they're already running
docker network connect c1-c2-bridge c1
docker network connect c1-c2-bridge c2

# if they're not running, then start them
docker container run --name c1 -d -it --network=c1-c2-bridge alpine
docker container run --name c2 -d -it --network=c1-c2-bridge alpine
```
- check containers from a network: `docker network inspect c1-c2-bridge`

### Volumes
#### Bind mounts
```bash
mkdir docker-demo
cd docker-demo

docker run --rm -v $(pwd):/data alpine sh -c "echo hello > /data/test.txt" 

# then check the file in the current directory
cat test.txt
```

#### Named volumes
```bash
docker volume create test-volume
docker run --rm -v test-volume:/data alpine sh -c "echo hello > /data/test.txt"

# create another container with the same volume
docker run --rm -v test-volume:/data alpine cat /data/test.txt
```
