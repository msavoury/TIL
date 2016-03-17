# TIL
Collection of Useful Things Learned

- Install ```rlwrap``` to get history for readlines that don't support it (mit-scheme)

Python Simple Server:
```
python -m SimpleHTTPServer
```

##Command line shortcuts
- Ctrl P : Previous entry in history
- Ctrl N : Next entry in history
- Ctrl B : Move back one char
- Ctrl F : Move forward one char
- Alt  F : Move forward one word
- Alt  B : Move backward one word
- Ctrl A : Move cursor to beginning of line
- Ctrl E : Move cursor to end of line
- Ctrl K : Delete from cursor to end of line
- Ctrl U : Delete from cursor to beginning of line
- Ctrl W : Delete word
- Ctrl [ : Escape
- Ctrl T : Transpose chars
- Ctrl C : Cancel line

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


### Check containers status
```
docker ps -a
```

### Starting a stopped container
```
docker start <container-name>
```
### Attach to a running container
```
docker attach <container-name>
```

### View logs for container
```
docker logs <container-name>
docker logs -f <container-name> # follow logs
docker logs -t <container-name> # prefix with timestamps
```

### Inspect processes on container
```
docker top <container-name>
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

