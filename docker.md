#Docker
Check docker status
```
sudo status docker
sudo start docker
sudo stop docker
sudo docker info
```
###Build a docker image from an existing Dockerfile
```
SYNTAX:
docker build -t <image-name> <path-to-dir-containing-dockerfile>
EXAMPLE:
docker build -t myCoolImage .
```

### Create a new container 
```
docker run -t -i --name my-container-name ubuntu /bin/sh
```
- ```-t``` open up a tty
- ```-i``` keep STDIN open on container
- ```/bin/sh``` run this command on creation (giving us a prompt)

### Create a new container in daemon mode
```
docker run -d --name my-container-name ubuntu /bin/sh
```


### List all containers 
```
docker ps -a
```

### Starting a stopped container
```
docker start <container-name>
```
### Attach to a running container
```
docker attach <container-name> # Use Ctrl P Ctrl Q to exit
OR
docker exec -it <container-name> bash #Type 'exit' to exit
```

### View logs for container
```
docker logs <container-name>
docker logs -f <container-name> # follow logs
docker logs -t <container-name> # prefix with timestamps
```

### Inspect container
```
docker top <container-name> #inspect processes
docker inspect <container-name> #inspect container itself
```
### Statistics for container
```
docker stats <container-name>
```
### Run process in container
```
docker exec -d <container-name> <command> # run in background
docker exec -t -i <container-name> /bin/sh # open prompt and run commands interactively
```
### Stopping a daemonized container
```
docker stop <container-name>
```
### Remove a container
```
docker rm <container-name>
```

##Command recap (needs cleanup)
Docker management commands
Command	Description
- dockerd	Launch the Docker daemon
- info	Display system-wide information
- inspect	Return low-level information on a container or image
- version	Show the Docker version information
Image commands
Command	Description
- build	Build an image from a Dockerfile
- commit	Create a new image from a container’s changes
- export	Export a container’s filesystem as a tar archive
- history	Show the history of an image
- images	List images
- import	Import the contents from a tarball to create a filesystem image
- load	Load an image from a tar archive or STDIN
- rmi	Remove one or more images
- save	Save images to a tar archive
- tag	Tag an image into a repository
Container commands
Command	Description
- attach	Attach to a running container
- cp	Copy files/folders from a container to a HOSTDIR or to STDOUT
- create	Create a new container
- diff	Inspect changes on a container’s filesystem
- events	Get real time events from the server
- exec	Run a command in a running container
- kill	Kill a running container
- logs	Fetch the logs of a container
- pause	Pause all processes within a container
- port	List port mappings or a specific mapping for the container
- ps	List containers
- rename	Rename a container
- restart	Restart a running container
- rm	Remove one or more containers
- run	Run a command in a new container
- start	Start one or more stopped containers
- stats	Display a live stream of container(s) resource usage statistics
- stop	Stop a running container
- top	Display the running processes of a container
- unpause	Unpause all processes within a container
- update	Update configuration of one or more containers
- wait	Block until a container stops, then print its exit code
Hub and registry commands
Command	Description
- login	Register or log in to a Docker registry
- logout	Log out from a Docker registry
- pull	Pull an image or a repository from a Docker registry
- push	Push an image or a repository to a Docker registry
- search	Search the Docker Hub for images
Network and connectivity commands
Command	Description
- network connect	Connect a container to a network
- network create	Create a new network
- network disconnect	Disconnect a container from a network
- network inspect	Display information about a network
- network ls	Lists all the networks the Engine daemon knows about
- network rm	Removes one or more networks
Shared data volume commands
Command	Description
- volume create	Creates a new volume where containers can consume and store data
- volume inspect	Display information about a volume
- volume ls	Lists all the volumes Docker knows about
- volume rm	Remove one or more volumes
