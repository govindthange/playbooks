# How to setup GitHub like centralized repository on premise?

## Step 1. Install docker.

```
govind@thinkpad:~$ sudo apt install docker.io
Reading package lists... Done
Building dependency tree       
Reading state information... Done
.
.
Adding group `docker' (GID 135) ...
Done.
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /li
b/systemd/system/docker.service.
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/sy
stemd/system/docker.socket.
Processing triggers for systemd (245.4-4ubuntu3.19) ...
Processing triggers for man-db (2.9.1-1) ...
```

## Step 2. Test the docker installation.

#### Check the docker version.

```
govind@thinkpad:~$ docker -v
Docker version 20.10.12, build 20.10.12-0ubuntu2~20.04.1
```

#### Run hello-world to test whether docker is able to pull image and create its container.

```
govind@thinkpad:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:6e8b6f026e0b9c419ea0fd02d3905dd0952ad1feea67543f525c73a0a790fefb
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## Step 3. Install GitBucket image.

Pull the `GitBucket` image

```
govind@thinkpad:~$ sudo docker pull gitbucket/gitbucket
Using default tag: latest
latest: Pulling from gitbucket/gitbucket
f3ef4ff62e0d: Pull complete 
706b9b9c1c44: Pull complete 
2ceb9f28e071: Pull complete 
c293b8888750: Pull complete 
b283545a6304: Pull complete 
Digest: sha256:0a53c4d606027661d08530d83143e3a2863b4b320ee436aaa088025d737be0dc
Status: Downloaded newer image for gitbucket/gitbucket:latest
docker.io/gitbucket/gitbucket:latest
```

### Option 1. Run GitBucket w/ volume (recommended)

```
govind@thinkpad:~$ sudo docker run -d -p 8080:8080 -v `pwd`/gitbucket:/gitbucket gitbucket/gitbucket
5c11050c743f5f69585b1b3cc87388ab6e4fc5f9774a3969ca100b00bbb8fd00
```


### Option 2. Run GitBucket w/o volume (for testing)

```
govind@thinkpad:~$ sudo docker run -d -p 8080:8080  gitbucket/gitbucket
5c11050c743f5f69585b1b3cc87388ab6e4fc5f9774a3969ca100b00bbb8fd00
```

# Step 4. Verify that GitBucket container is running.

```
govind@thinkpad:~$ sudo docker container list
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
5c11050c743f   gitbucket/gitbucket   "sh -c 'java -jar /o…"   18 seconds ago   Up 16 seconds   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp, 29418/tcp   infallible_swirles
govind@thinkpad:~$ 
```
