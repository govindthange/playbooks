# How to install docker?

## Step 1. Set up Docker's package repository
[...](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

#### 1. Add Docker's official GPG key:
```
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Example:
```
govind@thinkpad:~/Downloads$ sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
[sudo] password for govind: 
Hit:1 https://brave-browser-apt-release.s3.brave.com stable InRelease
Hit:2 https://packages.microsoft.com/repos/code stable InRelease                          
Hit:3 https://download.docker.com/linux/ubuntu jammy InRelease                            
Hit:4 http://apt.pop-os.org/proprietary jammy InRelease                                   
Hit:5 http://apt.pop-os.org/release jammy InRelease
Hit:6 http://apt.pop-os.org/ubuntu jammy InRelease
Get:7 http://apt.pop-os.org/ubuntu jammy-security InRelease [110 kB]
Get:8 http://apt.pop-os.org/ubuntu jammy-updates InRelease [119 kB]
Hit:9 http://apt.pop-os.org/ubuntu jammy-backports InRelease
Get:10 http://apt.pop-os.org/ubuntu jammy-updates/universe Sources [316 kB]
Get:11 http://apt.pop-os.org/ubuntu jammy-updates/universe i386 Packages [695 kB]
Get:12 http://apt.pop-os.org/ubuntu jammy-updates/universe amd64 Packages [1,057 kB]
Fetched 2,297 kB in 7s (343 kB/s)                                                         
Reading package lists... Done
N: Skipping acquire of configured file 'main/binary-i386/Packages' as repository 'https://brave-browser-apt-release.s3.brave.com stable InRelease' doesn't support architecture 'i386'
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ca-certificates is already the newest version (20230311ubuntu0.22.04.1).
curl is already the newest version (7.81.0-1ubuntu1.15).
0 upgraded, 0 newly installed, 0 to remove and 4 not upgraded.
```

#### 2. Add the repository to Apt sources:

> If you use an Ubuntu derivative distro, such as Linux Mint, you may need to use UBUNTU_CODENAME instead of VERSION_CODENAME.

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$UBUNTU_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Ubuntu Example:

```
govind@thinkpad:~/Downloads$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$UBUNTU_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
Hit:1 https://brave-browser-apt-release.s3.brave.com stable InRelease
Hit:2 https://download.docker.com/linux/ubuntu jammy InRelease                            
Hit:3 https://packages.microsoft.com/repos/code stable InRelease                          
Hit:4 http://apt.pop-os.org/proprietary jammy InRelease                                   
Hit:5 http://apt.pop-os.org/release jammy InRelease
Hit:6 http://apt.pop-os.org/ubuntu jammy InRelease
Hit:7 http://apt.pop-os.org/ubuntu jammy-security InRelease
Hit:8 http://apt.pop-os.org/ubuntu jammy-updates InRelease
Hit:9 http://apt.pop-os.org/ubuntu jammy-backports InRelease
Reading package lists... Done
N: Skipping acquire of configured file 'main/binary-i386/Packages' as repository 'https://brave-browser-apt-release.s3.brave.com stable InRelease' doesn't support architecture 'i386'
```

Pop OS Example:

```
govind@thinkpad:~/Downloads$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$UBUNTU_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
Hit:1 https://packages.microsoft.com/repos/code stable InRelease
Hit:2 https://brave-browser-apt-release.s3.brave.com stable InRelease                     
Hit:3 https://download.docker.com/linux/ubuntu jammy InRelease                            
Hit:4 http://apt.pop-os.org/proprietary jammy InRelease                
Hit:5 http://apt.pop-os.org/release jammy InRelease
Hit:6 http://apt.pop-os.org/ubuntu jammy InRelease
Hit:7 http://apt.pop-os.org/ubuntu jammy-security InRelease
Hit:8 http://apt.pop-os.org/ubuntu jammy-updates InRelease
Hit:9 http://apt.pop-os.org/ubuntu jammy-backports InRelease
Reading package lists... Done
N: Skipping acquire of configured file 'main/binary-i386/Packages' as repository 'https://brave-browser-apt-release.s3.brave.com stable InRelease' doesn't support architecture 'i386'

```
## Step 2. Install the `Docker` packages

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Step 3. Verify `Docker Engine` installation is successful

```
sudo docker run hello-world
```

## Step 4. Setup docker for non-root user
[...](https://docs.docker.com/engine/install/linux-postinstall/)

#### 1. Check existing groups

```
govind@thinkpad:~/Downloads$ groups
govind adm sudo lpadmin
```

#### 2. Create the `docker` group

```
sudo groupadd docker
```

#### 3. Add your user to the `docker` group

```
sudo usermod -aG docker $USER
```

#### 4. Activate the changes to group

```
newgrp docker
```


#### 5. Confirm whether `docker` is listed in `groups` command
```
govind@thinkpad:~/Downloads$ groups
docker adm sudo lpadmin govind
```

#### 6. Verify that you can run docker commands without sudo
```
docker run hello-world
```

#### 7. Fix permission settings for the `~/.docker/` directory

```
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R
```

## Step 5. Configure Docker to start on boot with systemd

```
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

To stop the behavior, use `disable` instead

```
sudo systemctl disable docker.service
sudo systemctl disable containerd.service
```

## Step 6. Restart machine

## Step 7. Update system

```
govind@thinkpad:~$ sudo apt-get update
[sudo] password for govind: 
Hit:1 https://brave-browser-apt-release.s3.brave.com stable InRelease
Hit:2 https://download.docker.com/linux/ubuntu jammy InRelease                                    
Hit:3 https://packages.microsoft.com/repos/code stable InRelease                                  
Hit:4 http://apt.pop-os.org/proprietary jammy InRelease
Hit:5 http://apt.pop-os.org/release jammy InRelease
Hit:6 http://apt.pop-os.org/ubuntu jammy InRelease
Get:7 http://apt.pop-os.org/ubuntu jammy-security InRelease [110 kB]
Get:8 http://apt.pop-os.org/ubuntu jammy-updates InRelease [119 kB]
Hit:9 http://apt.pop-os.org/ubuntu jammy-backports InRelease
Fetched 229 kB in 3s (76.5 kB/s)
Reading package lists... Done
N: Skipping acquire of configured file 'main/binary-i386/Packages' as repository 'https://brave-browser-apt-release.s3.brave.com stable InRelease' doesn't support architecture 'i386'
```

## Step 8. Install package

#### 1. Install .deb file

```
govind@thinkpad:~$ sudo apt-get install ./Downloads/docker-desktop-4.28.0-amd64.deb 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Note, selecting 'docker-desktop' instead of './Downloads/docker-desktop-4.28.0-amd64.deb'
The following additional packages will be installed:
  cpu-checker ibverbs-providers ipxe-qemu ipxe-qemu-256k-compat-efi-roms libcacard0 libdaxctl1 libfdt1 libgfapi0 libgfrpc0 libgfxdr0 libglusterfs0 libibverbs1 libiscsi7 libndctl6
  libpmem1 libpmemobj1 libqrencode4 librados2 librbd1 librdmacm1 libspice-server1 liburing2 libusbredirparser1 libvirglrenderer1 msr-tools ovmf pass qemu-block-extra
  qemu-system-common qemu-system-data qemu-system-gui qemu-system-x86 qemu-utils qrencode seabios tree uidmap xclip
Suggested packages:
  gstreamer1.0-libav gstreamer1.0-plugins-ugly libxml-simple-perl python ruby samba vde2 debootstrap
The following NEW packages will be installed:
  cpu-checker docker-desktop ibverbs-providers ipxe-qemu ipxe-qemu-256k-compat-efi-roms libcacard0 libdaxctl1 libfdt1 libgfapi0 libgfrpc0 libgfxdr0 libglusterfs0 libibverbs1
  libiscsi7 libndctl6 libpmem1 libpmemobj1 libqrencode4 librados2 librbd1 librdmacm1 libspice-server1 liburing2 libusbredirparser1 libvirglrenderer1 msr-tools ovmf pass
  qemu-block-extra qemu-system-common qemu-system-data qemu-system-gui qemu-system-x86 qemu-utils qrencode seabios tree uidmap xclip
0 upgraded, 39 newly installed, 0 to remove and 4 not upgraded.
Need to get 0 B/407 MB of archives.
After this operation, 123 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
...
```

#### 2. Check the versions of installed binaries

```
govind@thinkpad:~$ docker compose version
Docker Compose version v2.24.6-desktop.1
govind@thinkpad:~$ docker --version
Docker version 25.0.4, build 1a576c5
govind@thinkpad:~$ docker version
Client: Docker Engine - Community
 Cloud integration: v1.0.35+desktop.11
 Version:           25.0.4
 API version:       1.44
 Go version:        go1.21.8
 Git commit:        1a576c5
 Built:             Wed Mar  6 16:32:12 2024
 OS/Arch:           linux/amd64
 Context:           desktop-linux

Server: Docker Desktop 4.28.0 (139021)
 Engine:
  Version:          25.0.3
  API version:      1.44 (minimum version 1.24)
  Go version:       go1.21.6
  Git commit:       f417435
  Built:            Tue Feb  6 21:14:25 2024
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.28
  GitCommit:        ae07eda36dd25f8a1b98dfbf587313b99c0190bb
 runc:
  Version:          1.1.12
  GitCommit:        v1.1.12-0-g51d5e94
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

## Step 9. Launch `Docker Desktop`

```
govind@thinkpad:~$ systemctl --user start docker-desktop
```

## Step 10. Setup `Docker Desktop`

---

## Upgrade Docker Desktop

Once a new version for Docker Desktop is released, the Docker UI shows a notification. You need to download the new package each time you want to upgrade Docker Desktop and run:

```
sudo apt-get install ./docker-desktop-<version>-<arch>.deb
```