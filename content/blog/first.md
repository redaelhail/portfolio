---
title: "Docker Cheat Sheet"
date:  Sun May 15, 2022
draft: false
---


## Containers

### Stop all Docker containers

Stop all Docker containers:
```bash
docker kill $(docker ps -q)
```

### Remove all Docker containers

Remove all Docker containers:
```bash
docker rm $(docker ps -a -q)
```

### Remove all Docker images

Remove all Docker images:
```bash
docker rmi $(docker images -q)
```

The `-q` flag will only list the IDs for those containers.

## Volumes

### Create volume

Create volume:
```bash
docker volume create name_volume
```

This will automatically create a volume mounted on the specified container:
```bash
docker run -it -v name_of_volume klakegg/hugo "new site web"
```

### Remove volume

Remove volume:
```bash
docker volume rm name_volume
```

### Remove container and corresponding volumes

Remove container and corresponding volumes:
```bash
docker rm -v mysql_db_1
```

If we don’t remove the anonymous volume and the container together, it becomes a dangling volume. We can list and remove all the dangling volumes using the following commands:
```bash
docker volume ls -qf dangling=true
docker volume rm $(docker volume ls -qf dangling=true)
```

### Remove unused volumes

Remove unused volumes:
```bash
docker volume prune
```

### List volumes

List volumes:
```bash
docker volume ls
```

## Bind Mounts

It is possible to start a container with bind mounts. This allows us to use a container to run tools that we don't want to install on our host, and yet still work with our host's files:
```bash
docker run -v $(pwd):/var/opt/project bash:latest bash -c "echo Hello > /var/opt/project/file.txt"
```

## Inspect Volume

### Inspect volume

Inspect volume:
```bash
docker volume inspect <volume_name>
```


