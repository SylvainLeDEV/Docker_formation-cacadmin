# Docker Training - Cocadmin

## Command Glossary

```bash
docker container run 
# or 
docker run 

docker container ls 
# or 
docker ps 
```

### Adding Options to Commands

To add options to commands, always place them before the image name. 

Example:

```bash 
docker run -ti ubuntu bash
```
- `-t`: Allocates a pseudo-TTY (terminal)
- `-i`: Runs in interactive mode

### Tagging Images

To tag an image, use:

```bash 
docker image tag ubuntu myimage:mytag
```

## Networking

To access a container externally via HTTP, like a server, you need to map the host machine's ports to the container's ports.

```bash 
docker run -ti -p 7777:7777 python:2 python -m SimpleHTTPServer 7777
```

### List Networks

```bash
docker network ls 
```

### Creating a Network

To create a new network:

```bash
docker network create sylvain
```

### Connecting a Container to a Network

To connect a container to a network:

```bash
docker network connect <NetworkID> <ContainerID>
```

### Accessing a Network

To run a container in a specific network:

```bash 
docker run -ti -p 7778:7777 --network sylvain python:2 python -m SimpleHTTPServer 7777
```

## Docker Inspect

This command returns a JSON object containing all the information about a container.

## Volumes

You can create persistent volumes using the following command:

```bash 
docker volume create myvolume
```

### Attaching a Volume

To attach a volume to a container:

```bash 
docker run -ti --name alice --volume myvolume:/tmp/project ubuntu bash
```

Once inside the container, create a file in `/tmp/project`, then restart another container with the same volume to see that the file is shared:

```bash 
docker run -ti --name bob --volume myvolume:/tmp/project ubuntu bash
```

### Sharing a Host Volume with a Container

To share a volume from your host machine with a container:

```bash 
docker run -ti --volume /Users/sylvain/.ssh:/root/.ssh ubuntu bash
```
