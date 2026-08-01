---
title: Docker 
parent: Misc
grand_parent: Linux
nav_order: 150
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

--- 

# Docker Command Reference

Quick-reference snippets for common Docker tasks.


## Container Lifecycle

### Run container

*Use:* run container.

```bash
docker run -d --name nginx -p 80:80 nginx
```

### Interactive shell

*Use:* interactive shell.

```bash
docker run -it ubuntu:24.04 bash
```

### Stop

*Use:* stop.

```bash
docker stop mycontainer
```

### Start

*Use:* start.

```bash
docker start mycontainer
```

### Restart

*Use:* restart.

```bash
docker restart mycontainer
```

### Kill

*Use:* kill.

```bash
docker kill mycontainer
```

### Remove

*Use:* remove.

```bash
docker rm mycontainer
```

### Force remove

*Use:* force remove.

```bash
docker rm -f mycontainer
```

### Auto remove

*Use:* auto remove.

```bash
docker run --rm alpine echo hello
```


## Images

### List images

*Use:* list images.

```bash
docker images
```

### Pull image

*Use:* pull image.

```bash
docker pull postgres:16
```

### Build image

*Use:* build image.

```bash
docker build -t myapp:latest .
```

### Build from Dockerfile

*Use:* build from dockerfile.

```bash
docker build -f Dockerfile.dev -t myapp:dev .
```

### Tag image

*Use:* tag image.

```bash
docker tag myapp:latest registry.example.com/myapp:v1
```

### Push image

*Use:* push image.

```bash
docker push registry.example.com/myapp:v1
```

### Remove image

*Use:* remove image.

```bash
docker rmi myapp:latest
```

### Prune dangling

*Use:* prune dangling.

```bash
docker image prune
```

### Prune all unused

*Use:* prune all unused.

```bash
docker image prune -a
```


## Containers

### List running

*Use:* list running.

```bash
docker ps
```

### List all

*Use:* list all.

```bash
docker ps -a
```

### Inspect

*Use:* inspect.

```bash
docker inspect mycontainer
```

### Stats

*Use:* stats.

```bash
docker stats
```

### Top

*Use:* top.

```bash
docker top mycontainer
```

### Logs

*Use:* logs.

```bash
docker logs mycontainer
```

### Follow logs

*Use:* follow logs.

```bash
docker logs -f mycontainer
```

### Last 100 lines

*Use:* last 100 lines.

```bash
docker logs --tail 100 mycontainer
```

### Execute command

*Use:* execute command.

```bash
docker exec mycontainer ls /
```

### Shell

*Use:* shell.

```bash
docker exec -it mycontainer bash
```

### Copy to container

*Use:* copy to container.

```bash
docker cp file.txt mycontainer:/tmp/
```

### Copy from container

*Use:* copy from container.

```bash
docker cp mycontainer:/tmp/file.txt .
```


## Networking

### List networks

*Use:* list networks.

```bash
docker network ls
```

### Inspect network

*Use:* inspect network.

```bash
docker network inspect bridge
```

### Create bridge

*Use:* create bridge.

```bash
docker network create mynet
```

### Connect

*Use:* connect.

```bash
docker network connect mynet mycontainer
```

### Disconnect

*Use:* disconnect.

```bash
docker network disconnect mynet mycontainer
```


## Volumes

### List volumes

*Use:* list volumes.

```bash
docker volume ls
```

### Create volume

*Use:* create volume.

```bash
docker volume create appdata
```

### Inspect volume

*Use:* inspect volume.

```bash
docker volume inspect appdata
```

### Remove volume

*Use:* remove volume.

```bash
docker volume rm appdata
```

### Prune unused

*Use:* prune unused.

```bash
docker volume prune
```

### Mount named volume

*Use:* mount named volume.

```bash
docker run -v appdata:/data busybox
```

### Bind mount

*Use:* bind mount.

```bash
docker run -v $(pwd):/app myapp
```


## Docker Compose

### Start stack

*Use:* start stack.

```bash
docker compose up -d
```

### Build & start

*Use:* build & start.

```bash
docker compose up -d --build
```

### Stop stack

*Use:* stop stack.

```bash
docker compose down
```

### Remove volumes

*Use:* remove volumes.

```bash
docker compose down -v
```

### Restart service

*Use:* restart service.

```bash
docker compose restart api
```

### View services

*Use:* view services.

```bash
docker compose ps
```

### Logs

*Use:* logs.

```bash
docker compose logs
```

### Follow logs

*Use:* follow logs.

```bash
docker compose logs -f api
```

### Execute shell

*Use:* execute shell.

```bash
docker compose exec api bash
```

### Run one-off

*Use:* run one-off.

```bash
docker compose run --rm api python manage.py
```

### Pull images

*Use:* pull images.

```bash
docker compose pull
```

### Rebuild

*Use:* rebuild.

```bash
docker compose build --no-cache
```

### Config check

*Use:* config check.

```bash
docker compose config
```


## Cleanup

### System prune

*Use:* system prune.

```bash
docker system prune
```

### Aggressive prune

*Use:* aggressive prune.

```bash
docker system prune -a --volumes
```

### Container prune

*Use:* container prune.

```bash
docker container prune
```

### Network prune

*Use:* network prune.

```bash
docker network prune
```

### Builder cache

*Use:* builder cache.

```bash
docker builder prune
```


## Registry

### Login

*Use:* login.

```bash
docker login
```

### Logout

*Use:* logout.

```bash
docker logout
```

### Search Hub

*Use:* search hub.

```bash
docker search nginx
```


## Dockerfile

### Copy files

*Use:* copy files.

```bash
COPY . /app
```

### Working directory

*Use:* working directory.

```bash
WORKDIR /app
```

### Run build step

*Use:* run build step.

```bash
RUN pip install -r requirements.txt
```

### Default command

*Use:* default command.

```bash
CMD ["python","app.py"]
```

### Entrypoint

*Use:* entrypoint.

```bash
ENTRYPOINT ["python"]
```

### Expose port

*Use:* expose port.

```bash
EXPOSE 8000
```

### Environment variable

*Use:* environment variable.

```bash
ENV APP_ENV=production
```

### Build argument

*Use:* build argument.

```bash
ARG VERSION=latest
```

### Switch user

*Use:* switch user.

```bash
USER appuser
```

### Healthcheck

*Use:* healthcheck.

```bash
HEALTHCHECK CMD curl -f http://localhost:8000 || exit 1
```


## Compose YAML

### Named volume

*Use:* named volume.

```bash
volumes:
  postgres_data:
```

### Environment

*Use:* environment.

```bash
.env
POSTGRES_DB=mydb
```

### Bind mount

*Use:* bind mount.

```bash
volumes:
  - ./data:/data
```

### Restart policy

*Use:* restart policy.

```bash
restart: unless-stopped
```

### Depends on

*Use:* depends on.

```bash
depends_on:
  - db
```

### Healthcheck

*Use:* healthcheck.

```bash
healthcheck:
  test: ["CMD","pg_isready","-U","postgres"]
```


## Debugging

### Events

*Use:* events.

```bash
docker events
```

### Disk usage

*Use:* disk usage.

```bash
docker system df
```

### Inspect mounts

*Use:* inspect mounts.

```bash
docker inspect mycontainer | grep Mounts -A10
```

### Processes

*Use:* processes.

```bash
docker top mycontainer
```

### Resource usage

*Use:* resource usage.

```bash
docker stats --no-stream
```


## Backup/Restore

### Export container

*Use:* export container.

```bash
docker export mycontainer > container.tar
```

### Import tar

*Use:* import tar.

```bash
docker import container.tar myimage
```

### Save image

*Use:* save image.

```bash
docker save postgres:16 -o postgres.tar
```

### Load image

*Use:* load image.

```bash
docker load -i postgres.tar
```


## Swarm (Basics)

### Init swarm

*Use:* init swarm.

```bash
docker swarm init
```

### Leave swarm

*Use:* leave swarm.

```bash
docker swarm leave
```

### List nodes

*Use:* list nodes.

```bash
docker node ls
```

### Deploy stack

*Use:* deploy stack.

```bash
docker stack deploy -c compose.yml mystack
```

### List stacks

*Use:* list stacks.

```bash
docker stack ls
```

### Remove stack

*Use:* remove stack.

```bash
docker stack rm mystack
```


## Useful Filters
```bash
docker ps --filter status=running
docker images --filter dangling=true
docker volume ls -q
docker network ls
```

## Useful Environment Variables
```bash
DOCKER_HOST
DOCKER_BUILDKIT=1
COMPOSE_PROJECT_NAME=myproject
```
