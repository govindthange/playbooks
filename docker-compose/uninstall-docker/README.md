# How to completely remove docker desktop and docker from Ubuntu/Pop OS?

## Step 1. Check all docker related installed packages

```
govind@thinkpad:~$ dpkg -l | grep -i docker
ii  docker-buildx-plugin                    0.13.0-1~ubuntu.22.04~jammy                                     amd64        Docker Buildx cli plugin.
ii  docker-ce                               5:25.0.4-1~ubuntu.22.04~jammy                                   amd64        Docker: the open-source application container engine
ii  docker-ce-cli                           5:25.0.4-1~ubuntu.22.04~jammy                                   amd64        Docker CLI: the open-source application container engine
ii  docker-ce-rootless-extras               5:25.0.4-1~ubuntu.22.04~jammy                                   amd64        Rootless support for Docker.
ii  docker-compose-plugin                   2.24.7-1~ubuntu.22.04~jammy                                     amd64        Docker Compose (V2) plugin for the Docker CLI.
```

## Step 2. Uninstall docker desktop

Remove docker-desktop

```
govind@thinkpad:~$ sudo apt purge docker-desktop
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  cpu-checker ibverbs-providers ipxe-qemu ipxe-qemu-256k-compat-efi-roms libcacard0 libdaxctl1 libfdt1 libgfapi0 libgfrpc0 libgfxdr0 libglusterfs0 libibverbs1 libiscsi7 libndctl6
  libpmem1 libpmemobj1 libqrencode4 librados2 librbd1 librdmacm1 libspice-server1 liburing2 libusbredirparser1 libvirglrenderer1 msr-tools ovmf pass qemu-block-extra
  qemu-system-common qemu-system-data qemu-system-gui qemu-system-x86 qemu-utils qrencode seabios tree uidmap xclip
Use 'sudo apt autoremove' to remove them.
The following packages will be REMOVED:
  docker-desktop*
0 upgraded, 0 newly installed, 1 to remove and 4 not upgraded.
After this operation, 0 B of additional disk space will be used.
Do you want to continue? [Y/n] yes
(Reading database ... 213088 files and directories currently installed.)
Purging configuration files for docker-desktop (4.28.0-139021) ...
Reloading systemd daemon for logged in users
Done reloading systemd daemon for logged in users
dpkg: warning: while removing docker-desktop, directory '/usr/local' not empty so not removed
```

## Step 3. Remove all docker related packages

```
sudo apt-get purge -y \
	docker-ce \
	docker-ce-cli \
	containerd.io \
	docker-buildx-plugin \
	docker-compose-plugin \
	docker-ce-rootless-extras \
	docker-engine \
	docker \
	docker.io

sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
	
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce  
```

## Step 4. Clean all traces of docker

```
sudo umount /var/lib/docker/
sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
sudo rm -rf /usr/bin/docker-compose
```

# Step 5. Confirm all docker components are removed

```
dpkg -l | grep -i docker
```

# Step 6. Restart machine
