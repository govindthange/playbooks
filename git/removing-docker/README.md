# How to remove docker?

## Step 1. List the docker package.

```
govind@thinkpad:~$ dpkg -l | grep -i docker
rc  docker.io                                  20.10.12-0ubuntu4                       amd64        Linux container runtime
```

## Step 2. Purge the docker.io.

```
govind@thinkpad:~$ sudo apt-get purge -y docker.io
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  docker.io*
0 upgraded, 0 newly installed, 1 to remove and 33 not upgraded.
After this operation, 0 B of additional disk space will be used.
(Reading database ... 202317 files and directories currently installed.)
Purging configuration files for docker.io (20.10.12-0ubuntu4) ...
```

## Step 3. Confirm that there is no docker listed.

```
govind@thinkpad:~$ dpkg -l | grep -i docker
```
