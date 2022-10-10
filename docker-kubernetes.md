<div align="center">
    <h1>Docker & Kubernetes</h1>
</div>

Docker version ```docker version``` 

Info ```docker info``` 

Docker commands list ```docker``` 

###### Alpine linux => A small security-focused distribution

#### Image vs Container

- An image is the application we want to run

- A contianer is an instance of that image running as a process

#### Run docker container

```shell
docker container run --name proxy --rm --publish 80:80 --detach nginx

Note:
--name --> container name
--rm --> remove container after close
nginx --> image name
--publish | -p --> port mapping
--detach | -d --> run container on background
```

#### List Docker container

```shell
# old way
docker ps

# new way
docker container ls

# list stopped container
docker container ls -a

# list container id
docker container ls -q
```

#### Stop docker container

```shell
# old way
docker stop <container_id / container_name>

# new way
docker container stop <container_id / container_name>
```

#### Container logs

```shell
# old way
docker logs <container_id / container_name>

# new way
docker container logs <container_id / container_name>
```

#### List process running inside container

```shell
docker container top <container_id / container_name>
```

#### Remove stopped container

```shell
docker container rm <container_id / container_name>

# remove all
docker container rm -f $(docker container -aq)

Note:
-f --> force delete don't care if the container is running
```

## What's going on in containers

- Process list in one container => ```docker container top <container_id / container_name>``` 

- Details of one container config => ```docker container inspect <container_id / container_name>``` 

- Performance stats for all containers => ```docker container stats``` 

## Getting a shell inside containers

- Start new container interactively => ```docker container run -it``` ```-t => simulates a real terminal``` ```-i => interactive and keep session open to receive terminal input``` 

- Run additional command in existing container => ```docker container exec -it <container_id / container_name> sh/bash``` 

## Container list format

```shell
cyan=$'\E[36m'
reset=$'\E[0m'
export FORMAT="$cyan ID$reset\t\t{{.ID}}\n
                $cyan NAME$reset\t\t{{.Names}}\n
                $cyan IMAGE$reset\t\t{{.Image}}\n
                $cyan PORTS$reset\t\t{{.Ports}}\n
                $cyan COMMAND$reset\t{{.Command}}\n
                $cyan CREATED$reset\t{{.CreatedAt}}\n
                $cyan STATUS$reset\t\t{{.Status}}\n"
```

#### Docker Networks

- Show networks => ```docker network ls``` 

- Inspect a network => ```docker network inspect <network_name>``` 

- Create a network => ```docker network create <network_name> <driver_name>``` 

- Attach a network to container => ```docker network connect <network_name>``` 

- Detach a network from container => ```docker network disconnect <network_name>``` 

13
