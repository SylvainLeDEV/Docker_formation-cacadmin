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

#### Background
Docker containers allow applications to run in isolation. You can add the -d option to run a container in the background, freeing up the terminal for other tasks. For example:
```bash
docker run -d image_name
```
Alternatively, by using the **tail -f** command, you can create an infinite loop that keeps the container active. This makes it easy to access the container for tasks like debugging or checking logs. Here's an example:

```bash
docker run image_name tail -f /dev/null
````
This method is especially useful when you need to interact with the container while it’s running.

## Conteneurs Docker

## Commandes pour gérer les conteneurs

- `docker containers ls`  # alias : `docker ps`
- `docker container run`   # alias : `docker run`
- `docker run hello-world`  # lance un conteneur avec l'image hello-world (téléchargée de Docker Hub)
- `docker run ubuntu echo cocadmin`  # télécharge l'image Ubuntu et lance `echo cocadmin` dans le conteneur Ubuntu
- `docker run -ti ubuntu bash`  # utilise l'image Ubuntu en cache et lance l'application bash en mode interactif
- `touch cocadmin`  # dans le conteneur
- `docker kill abc123`  # dans une autre fenêtre, stoppe le conteneur (tue le processus bash)
- `docker start -i abc123`  # redémarre le même conteneur 
- `ls`  # le fichier cocadmin est toujours là
- `exit`  # quitte l'application bash et stoppe le conteneur 
- `docker rm abc123`  # supprime le conteneur 
- `docker run -ti ubuntu bash`  # nouveau conteneur ID
- `docker ls`  # le fichier cocadmin n'est pas là

## Images

- `docker images ls`  # alias : `docker images`
- `docker pull python:2.7`  # télécharge l'image Python, tag 2.7
  - Un tag peut avoir n'importe quel nom (pas uniquement une version)
  - Sans `:2.7`, on aurait eu l'image **latest** (dernière) qui est alias de 3.7 (voir <https://hub.docker.com/_/ubuntu/>)
- `docker image rm python:2.7`  # on ne peut pas supprimer une image si un conteneur l'utilise (même stoppé)
- `docker image tag ubuntu monimage:montag`  # crée l'image `monimage:montag` copiée de `ubuntu:latest`
- `docker image ls`  # on voit ma nouvelle image avec le même ID

## Réseaux

Si un programme écoute sur un port, il écoute sur la carte réseau virtuelle créée par Docker. Pour y accéder depuis sa machine, il faut "mapper" un port de sa carte réseau avec un port de la carte virtuelle.

- `docker run -ti -p7777:7777 python:2 python -m SimpleHTTPServer 7777`  
  - `python -m SimpleHTTPServer 7777` démarre un serveur web qui sert les fichiers locaux.
  - On associe le port 7777 de la carte virtuelle avec celui de notre machine locale.
  - Sur Mac et PC, le port est associé avec la VM, qui est ensuite liée à la machine hôte. Cela est géré de manière transparente par l'application "Docker for Windows/Mac".

### Gestion des réseaux

- `docker network ls`  # affiche les réseaux par défaut avec 3 drivers
  - `bridge` = NAT
  - `host` = le conteneur utilise les ports de la carte réseau de l'hôte directement (un seul conteneur peut écouter chaque port)
  - `none` = pas de réseau

- `docker network create cocanet`  # crée le réseau cocanet
- `docker network connect cocanet abc123`  # ajoute une carte réseau au conteneur abc123
- `docker inspect abc123`  # à la fin, on voit que le conteneur a 2 cartes réseau : 1 dans `default` et l'autre dans `cocanet`

On aurait pu démarrer notre conteneur directement dans `cocanet` avec :

```bash
docker run -ti --network cocanet --name cocadmin -p7777:7777 python:2 python -m SimpleHTTPServer 7777
```

- On spécifie le réseau et un nom de conteneur.
- `docker inspect cocadmin`  # équivalent à abc123, cocadmin est juste dans le réseau cocanet.

## Volumes

On partage un volume entre 2 conteneurs.

- `docker volume create monvolume`
- `docker volume ls`
- `docker run -ti --name alice --volume monvolume:/tmp/projet ubuntu bash`
- `cd /tmp/projet`
- `touch fichier`

### Accès depuis un autre terminal

```bash
docker run -ti --name bob --volume monvolume:/tmp/projet ubuntu bash
cd /tmp/projet
ls
```

- On roule en arrière-plan. `-d` = daemon (tail -f).

On peut aussi créer des volumes pour qu'un conteneur accède aux fichiers de l'hôte :

```bash
docker run -ti --volume /home/coca/.ssh:/home/root/.ssh ubuntu bash
```

### Lancer en arrière-plan

```bash
docker run -d -p7777:7777 python:2 python -m SimpleHTTPServer 7777  # -d pour arrière-plan
docker run -d --name carol ubuntu tail -f  # lance un conteneur nommé **carol** en arrière-plan avec une commande qui ne finit jamais
docker exec -ti carol bash  # on démarre bash dans le conteneur **carol**
```

## Exercice

Lancer 3 conteneurs avec l'image CentOS et la commande `tail -f`, nommés `front`, `db` et `backend`.

- `backend` doit pouvoir pinger `front` et `db`, mais les deux premiers ne peuvent pinger que `backend` (front ne doit pas pouvoir pinger db et vice versa).

- `front` et `backend` doivent pouvoir partager des fichiers dans `/mnt/shared`.

### Création des réseaux et du volume partagé

```bash
docker network create front
docker network create db
docker network create backend
docker volume create shared
```

### Lancement des conteneurs

```bash
docker run -d --name front --network front --volume shared:/mnt/shared centos tail -f
docker run -d --name backend --network backend --volume shared:/mnt/shared centos tail -f
docker run -d --name db --network db centos tail -f
```

### Connexion de `backend` aux autres réseaux

```bash
docker network connect front backend
docker network connect db backend
```

### Tests de connectivité

```bash
docker exec front ping db  # ne peut pas résoudre
docker exec db ping front
docker exec backend ping front
docker exec backend ping db
docker exec front ping 172.21.0.2  # même avec l'adresse IP, ne peut toujours pas pinger
```

### Test du volume partagé

```bash
docker exec front touch /mnt/shared/lol
docker exec backend ls /mnt/shared/lol
```
