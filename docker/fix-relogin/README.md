# How to fix docker login issue?

## Step 1. Remove docker config.

Sometimes the token stored in config.json file becomes "stale" or corrupted, causing the CLI to think you’re logged in when the server says you aren't. Remove all Docker configuration settings, including authentication token like so:

```shell
govind@thinkpad:~/projects/kit/ai-langgraph-kit$ rm ~/.docker/config.json
```

## Step 2. Switch to desktop-linux context.

```shell
govind@thinkpad:~/projects/kit/ai-langgraph-kit$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                   KUBERNETES ENDPOINT   ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                                             
desktop-linux       moby                                                          unix:///home/govind/.docker/desktop/docker.sock                         

govind@thinkpad:~/projects/kit/ai-langgraph-kit$ docker context use desktop-linux 
desktop-linux
Current context is now "desktop-linux"
```

## Step 3. Login.

```shell
govind@thinkpad:~/projects/kit/ai-langgraph-kit$ sudo docker login -u thange
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

Logging in with your password grants your terminal complete access to your account. 
For better security, log in with a limited-privilege personal access token. Learn more at https://docs.docker.com/go/access-tokens/
```

## Step 4. Start using docker.

```shell
govind@thinkpad:~/projects/kit/ai-langgraph-kit$ ./relaunch.sh 
Relaunching Docker Compose stack...
[+] Building 1.8s (11/11) FINISHED                                                                                                                                               
 => [internal] load build definition from Dockerfile                                                                                                                        0.0s
 => => transferring dockerfile: 32B                                                                                                                                         0.0s
 => [internal] load .dockerignore                                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                                             0.0s
 => [internal] load metadata for docker.io/library/python:3.11-slim                                                                                                         1.6s
 => [internal] load build context                                                                                                                                           0.0s
 => => transferring context: 124B                                                                                                                                           0.0s
 => [1/6] FROM docker.io/library/python:3.11-slim@sha256:9358444059ed78e2975ada2c189f1c1a3144a5dab6f35bff8c981afb38946634                                                   0.0s
 => CACHED [2/6] WORKDIR /app                                                                                                                                               0.0s
 => CACHED [3/6] RUN apt-get update && apt-get install -y     build-essential     curl     && rm -rf /var/lib/apt/lists/*                                                   0.0s
 => CACHED [4/6] COPY requirements.txt .                                                                                                                                    0.0s
 => CACHED [5/6] RUN pip install --no-cache-dir -r requirements.txt                                                                                                         0.0s
 => CACHED [6/6] COPY . .                                                                                                                                                   0.0s
 => exporting to image                                                                                                                                                      0.0s
 => => exporting layers                                                                                                                                                     0.0s
 => => writing image sha256:ddaa0e4fbc9b44bffc5e410ea4cbb5fe25aa7afd936bfcb896bb09338eb33f7d                                                                                0.0s
 => => naming to docker.io/library/ai-langgraph-kit-app                                                                                                                     0.0s
[+] Running 1/1
 ⠿ Container langgraph-poc1  Started                                                                                                                                        2.0s
Done!
```
