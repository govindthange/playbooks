# How to check disk space usage on linux based systems?

Get information about the disk space usage on the system, including the file system name, total size, used space, available space, and mount point.

```
govind@thinkpad:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           3.2G  2.6M  3.2G   1% /run
efivarfs        196K   50K  142K  26% /sys/firmware/efi/efivars
/dev/nvme0n1p3  460G  321G  116G  74% /
tmpfs            16G  142M   16G   1% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/nvme0n1p1 1020M  266M  755M  27% /boot/efi
/dev/nvme0n1p2  4.0G  2.5G  1.6G  62% /recovery
tmpfs            16G     0   16G   0% /run/qemu
tmpfs           3.2G   34M  3.1G   2% /run/user/1000
```

Do as follows to display the disk space usage of all files and folders in the current directory in a readable format.

```
govind@thinkpad:~$ du -sh *
452K	2024-03-01 19-08-23.mkv
408M	books
37G	data
4.0K	Desktop
20M	docker-data
4.0K	docker-desktop-pass.md
213M	Documents
341M	Downloads
4.0K	Music
115M	Pictures
76K	pmo
12M	poc
8.0K	Postman
48G	projects
4.0K	Public
20K	snap
1.1G	system
13M	temp
149M	Templates
38G	Videos
```


