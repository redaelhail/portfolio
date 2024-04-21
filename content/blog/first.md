---
title: "docker"
date: 2022-05-15T13:24:31Z
draft: false
---

- Stop all docker containers: docker kill $(docker ps -q)
- Remove all docker containers: docker rm $(docker ps -a -q)
- Remmove all docker images:  docker rmi $(docker images -q)

The -q flag will only list the IDs for those containers.

## Volumes
# create volume
`docker volume create name_volume`

this will create automatically a volume mounted on the specified contaainer
`docker run -it  -v name of volume  klakegg/hugo  "new site web"`
# remove volume
`docker volume rm name_volume`
# REMOVE CONTTAINER AND COrresponding volumes
`docker rm -v mysql_db_1`
If we don’t remove the anonymous volume and the container together, it becomes a dangling volume.
We can list and remove all the dangling volumes using the following commands.

`docker volume ls -qf dangling=true`
`docker volume rm $(docker volume ls -qf dangling=true)`

# remove unused volumes
`docker rm prune`
# list volumes 
`docker  volume ls`
# Bind Mounts: 
It is possile to start a container with bind mounts.
 This allows us to use a container to run tools that we don't want to install on our host, and yet still work with our host's files: `docker run -v $(pwd):/var/opt/project bash:latest bash -c "echo Hello > /var/opt/project/file.txt"`

# inspect volume
` docker volume inspect`
