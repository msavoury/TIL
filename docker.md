##Docker
Check docker status
```
sudo status docker
sudo start docker
sudo stop docker
sudo docker info
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

