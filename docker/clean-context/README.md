# How to delete all images and containers from the current context?

## Step 1. Verify the context.

```bash
govind@thinkpad:~/projects/ml-wms$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                   KUBERNETES ENDPOINT   ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                                             
desktop-linux       moby                Docker Desktop    
```

## Step 2. Check containers and images to delete.

```bash
govind@thinkpad:~/projects/ml-wms$ docker ps -aq
bae03014b5d4
fb1f603c92aa

govind@thinkpad:~/projects/ml-wms$ docker images -aq
f346dda8bae1
70437a060f84
8a4684943b70
f6662a2a9fd0
a819979c05a6
0eb1d79e38bb
```

## Step 3. Stop containers and remove images.

```bash
docker ps -aq | xargs -r docker stop && docker ps -aq | xargs -r docker rm && docker images -aq | xargs -r docker rmi -f
```

## Step 4. Verify container and image count.

List all containers:

```bash
docker ps -aq
```
List all images:

```bash
docker images -aq
```
