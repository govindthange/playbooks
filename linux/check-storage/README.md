# How to check whether a specific folder has enough storage?

If you are in a folder and want to be 100% sure how much space it has before you copy that large file, use `df` command like so:

```bash
df -h .
```

The `.` tells `df` to report on the current directory.

```bash
govind@thinkpad:~$ df -h .
Filesystem      Size  Used Avail Use% Mounted on
/dev/nvme0n1p3  460G  421G   16G  97% /
```

Use `lsblk` to see the relationship between your physical disks and your folders i.e., exactly which partition is "hooked" into which folder.

```bash
lsblk
```

In your specific case, any folder listed in the **MOUNTPOINTS** column is a "gate" to a different partition.

```bash
govind@thinkpad:~$ lsblk
NAME          MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINTS
loop0           7:0    0  55.5M  1 loop  /snap/core18/2979
loop1           7:1    0  55.5M  1 loop  /snap/core18/2999
loop2           7:2    0    74M  1 loop  /snap/core22/2339
loop3           7:3    0    74M  1 loop  /snap/core22/2411
loop4           7:4    0   173M  1 loop  /snap/postman/248
loop5           7:5    0   173M  1 loop  /snap/postman/254
loop6           7:6    0   1.2G  1 loop  /snap/pycharm-community/592
loop7           7:7    0   1.2G  1 loop  /snap/pycharm-community/594
loop8           7:8    0  49.3M  1 loop  /snap/snapd/26865
loop9           7:9    0  48.4M  1 loop  /snap/snapd/26382
zram0         251:0    0    16G  0 disk  [SWAP]
nvme0n1       259:0    0 476.9G  0 disk  
├─nvme0n1p1   259:1    0  1022M  0 part  /boot/efi
├─nvme0n1p2   259:2    0     4G  0 part  /recovery
├─nvme0n1p3   259:3    0 467.9G  0 part  /
└─nvme0n1p4   259:4    0     4G  0 part  
  └─cryptswap 252:0    0     4G  0 crypt [SWAP]
```

Based on your `df` output:
* **`/`** is your main partition (`/dev/nvme0n1p3`).
* **`/recovery`** is a different partition.
* **`/boot/efi`** is a different partition.
* **Anything else** (like `/home`, `/etc`, `/opt`, or `/usr`) is physically located on your **`/`** partition.

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

# How to see disk space usage of all files and folders in the current directory in a readable format?

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

# How to see which folders are using the most space?

To see how much they are consuming, use the `du` (disk usage) command.

```bash
sudo du -h -x --max-depth=1 / | sort -hr
```

> **Note:** We use the `-x` flag to ensure the command stays on one filesystem and doesn't accidentally wander into your RAM disks (`tmpfs`) or other partitions.

```bash
govind@thinkpad:~$ sudo du -h -x --max-depth=1 ~/books/e-books | sort -hr
811M	/home/govind/books/e-books
525M	/home/govind/books/e-books/aiml
189M	/home/govind/books/e-books/architecture
60M	/home/govind/books/e-books/sql
33M	/home/govind/books/e-books/devops
2.7M	/home/govind/books/e-books/ba
```

**What this command does:**
* `sudo`: Required to access system folders like `/root` or `/var`.
* `du -h`: Displays sizes in "Human Readable" format (GB, MB).
* `-x`: **Crucial.** Tells the tool to stay on the current filesystem only.
* `--max-depth=1`: Limits the results to the top-level folders so you aren't buried in sub-folder data.
* `sort -hr`: Sorts the list by size, putting the biggest folders at the top.

