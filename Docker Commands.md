
---

# Docker Command Documentation

## Introduction

This documentation provides a reference for using Docker commands in both development and production environments. It includes commands for managing Docker containers, images, networks, and volumes.

---

## 1. **Basic Docker Commands**

### List Running Containers

To list all running containers:

```bash
docker ps
```

### List All Containers (Including Stopped)

To list all containers (including those that are stopped):

```bash
docker ps -a
```

### Inspect Container

To view detailed information about a container:

```bash
docker inspect <container_name_or_id>
```

### View Container Logs

To view logs for a specific container:

```bash
docker logs <container_name_or_id>
```

---

## 2. **Docker Image Commands**

### List All Images

To list all available images:

```bash
docker images
```

### Pull Image from Docker Hub

To pull an image from Docker Hub (or a custom registry):

```bash
docker pull <image_name>
```

### Build Image from Dockerfile

To build a Docker image from a Dockerfile:

```bash
docker build -t <image_name> <path_to_dockerfile>
```

### Remove Docker Image

To remove an image:

```bash
docker rmi <image_name_or_id>
```

---

## 3. **Container Commands**

### Run a New Container

To run a new container:

```bash
docker run <options> <image_name_or_id>
```

Example: Run a container in detached mode with port mapping:

```bash
docker run -d -p 80:80 <image_name>
```

### Execute Command Inside Running Container

To run a command inside a running container:

```bash
docker exec -it <container_name_or_id> <command>
```

Example: Run a bash shell inside the container:

```bash
docker exec -it <container_name_or_id> bash
```

### Stop a Running Container

To stop a running container:

```bash
docker stop <container_name_or_id>
```

### Start a Stopped Container

To start a stopped container:

```bash
docker start <container_name_or_id>
```

### Restart a Container

To restart a running or stopped container:

```bash
docker restart <container_name_or_id>
```

### Remove a Container

To remove a container (stopped or running):

```bash
docker rm <container_name_or_id>
```

### View Container Stats

To view CPU, memory, and other stats for a container:

```bash
docker stats <container_name_or_id>
```

---

## 4. **Docker Compose Commands**

### Start All Containers

To start all containers defined in a `docker-compose.yml` file:

```bash
docker-compose up
```

### Start Containers in Detached Mode

To start containers in the background (detached mode):

```bash
docker-compose up -d
```

### Stop All Containers

To stop all containers defined in a `docker-compose.yml` file:

```bash
docker-compose down
```

### View Logs from All Containers

To view logs from all containers in the Compose setup:

```bash
docker-compose logs
```

### Run One-off Commands

To run one-off commands (e.g., for migrations):

```bash
docker-compose run <service_name> <command>
```

---

## 5. **Docker Volume Commands**

### List All Volumes

To list all Docker volumes:

```bash
docker volume ls
```

### Create a Volume

To create a new Docker volume:

```bash
docker volume create <volume_name>
```

### Remove a Volume

To remove a Docker volume:

```bash
docker volume rm <volume_name>
```

---

## 6. **Docker Network Commands**

### List All Networks

To list all Docker networks:

```bash
docker network ls
```

### Create a Custom Network

To create a custom Docker network:

```bash
docker network create <network_name>
```

### Remove a Network

To remove a Docker network:

```bash
docker network rm <network_name>
```

---

## 7. **Docker System Cleanup Commands**

### Remove Unused Data (Images, Containers, Volumes, Networks)

To remove unused images, containers, volumes, and networks:

```bash
docker system prune
```

### Remove Stopped Containers

To remove only stopped containers:

```bash
docker container prune
```

### Remove Unused Volumes

To remove only unused volumes:

```bash
docker volume prune
```

### Remove Unused Networks

To remove only unused networks:

```bash
docker network prune
```

---

## 8. **Advanced Docker Commands**

### View Docker Daemon Information

To view detailed information about the Docker daemon:

```bash
docker info
```

### Save a Docker Image to a Tar File

To save a Docker image to a tar archive:

```bash
docker save -o <image_name>.tar <image_name_or_id>
```

### Load a Docker Image from a Tar File

To load a Docker image from a tar archive:

```bash
docker load -i <image_name>.tar
```

---

## 9. **Docker Help Commands**

### Get Help for a Docker Command

To get detailed help for any Docker command:

```bash
docker <command> --help
```

Example: To get help with the `docker run` command:

```bash
docker run --help
```

---

## Conclusion

This documentation provides the most commonly used Docker commands and their examples. You can expand this document by adding more specific commands for advanced Docker operations as you explore more complex Docker configurations.

---

You can save this content as a `.md` file and include it in your project documentation. Let me know if you'd like further enhancements or additions to the document!