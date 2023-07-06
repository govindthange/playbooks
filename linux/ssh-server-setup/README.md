# How to setup SSH server on Ubuntu 22.04.1 LTS?

## Step 1. Update package lists.

Run the following command to update the package lists on your system:

```
govind@thinkpad:~$ sudo apt-get update
Get:1 https://linux.teamviewer.com/deb stable InRelease [11.9 kB]
Hit:2 https://download.docker.com/linux/ubuntu jammy InRelease                 
Hit:3 http://packages.microsoft.com/repos/code stable InRelease                
Get:4 https://packages.microsoft.com/repos/ms-teams stable InRelease [5,931 B] 
Hit:5 http://in.archive.ubuntu.com/ubuntu jammy InRelease                      
Get:6 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]      
Hit:7 https://ppa.launchpadcontent.net/obsproject/obs-studio/ubuntu jammy InRelease
Get:8 http://security.ubuntu.com/ubuntu jammy-security/main amd64 DEP-11 Metadata [41.5 kB]
Get:9 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 DEP-11 Metadata [22.0 kB]
Fetched 192 kB in 1s (145 kB/s)                         
Reading package lists... Done
```

This command refreshes the package information, ensuring that you have the latest version of available packages.

## Step 2. Fix broken packages.

Run the following command to attempt to fix any broken or unmet dependencies:

```
govind@thinkpad:~$ sudo apt-get install -f
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

This command tries to resolve dependency issues and fix any broken packages on your system.

## Step 3. Upgrade packages.

Run the following command to upgrade all installed packages to their latest versions:

```
govind@thinkpad:~$ sudo apt-get upgrade 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

This command ensures that all packages on your system are up to date.

## Step 4. Install `openssh-server`

After performing the previous steps, attempt to install the openssh-server package again using the following command:

```
sudo apt-get install openssh-server
```

This command should now be able to install the SSH server without encountering unmet dependency errors.

---

# Troubleshooting

## `Ubuntu 22.04 openssh-server` dependendency on `libc6:i386`

If you run int libc6 dependency issue then refer [this](https://medium.com/@lj1212.up/ubuntu-22-04-openssh-server-%EC%84%A4%EC%B9%98-%EC%8B%A4%ED%8C%A8-30a6e231c614) page for fix.

```
govind@thinkpad:~$ sudo apt-get install openssh-server
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 libbsd0:i386 : Depends: libc6:i386 (>= 2.33) but it is not installable
 libcbor0.8:i386 : Depends: libc6:i386 (>= 2.4) but it is not installable
 libcom-err2:i386 : Depends: libc6:i386 (>= 2.28) but it is not installable
 libedit2:i386 : Depends: libc6:i386 (>= 2.33) but it is not installable
 libfido2-1:i386 : Depends: libc6:i386 (>= 2.33) but it is not installable
 libgssapi-krb5-2:i386 : Depends: libc6:i386 (>= 2.33) but it is not installable
 libk5crypto3:i386 : Depends: libc6:i386 (>= 2.33) but it is not installable
 libkeyutils1:i386 : Depends: libc6:i386 (>= 2.7) but it is not installable
 libkrb5-3:i386 : Depends: libc6:i386 (>= 2.34) but it is not installable
 libkrb5support0:i386 : Depends: libc6:i386 (>= 2.34) but it is not installable
 libmd0:i386 : Depends: libc6:i386 (>= 2.33) but it is not installable
 libpcre2-8-0:i386 : Depends: libc6:i386 (>= 2.4) but it is not installable
 libselinux1:i386 : Depends: libc6:i386 (>= 2.34) but it is not installable
 libssl3:i386 : Depends: libc6:i386 (>= 2.34) but it is not installable
 libtinfo6:i386 : Depends: libc6:i386 (>= 2.33) but it is not installable
 libudev1:i386 : Depends: libc6:i386 (>= 2.34) but it is not installable
 openssh-client:i386 : Depends: libc6:i386 (>= 2.34) but it is not installable
 zlib1g:i386 : Depends: libc6:i386 (>= 2.4) but it is not installable
E: Unable to correct problems, you have held broken packages.
```

### Step 1. Install `libc6` to fix above issue.

```
govind@thinkpad:~$ sudo apt install libc6=2.35-0ubuntu3
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  libc-dev-bin libc-devtools libcrypt-dev libnsl-dev libtirpc-dev
  linux-libc-dev rpcsvc-proto
Use 'sudo apt autoremove' to remove them.
Suggested packages:
  glibc-doc
Recommended packages:
  libnss-nis libnss-nisplus
The following packages will be REMOVED:
  build-essential g++ g++-11 libc6-dbg libc6-dev libstdc++-11-dev
The following packages will be DOWNGRADED:
  libc6
0 upgraded, 0 newly installed, 1 downgraded, 6 to remove and 0 not upgraded.
Need to get 3,235 kB of archives.
After this operation, 80.7 MB disk space will be freed.
Do you want to continue? [Y/n] Y
Get:1 http://in.archive.ubuntu.com/ubuntu jammy/main amd64 libc6 amd64 2.35-0ubuntu3 [3,235 kB]
Fetched 3,235 kB in 2s (1,863 kB/s)
Preconfiguring packages ...
(Reading database ... 246124 files and directories currently installed.)
Removing build-essential (12.9ubuntu3) ...
Removing g++ (4:11.2.0-1ubuntu1) ...
Removing g++-11 (11.3.0-1ubuntu1~22.04.1) ...
Removing libc6-dbg:amd64 (2.35-0ubuntu3.1) ...
Removing libstdc++-11-dev:amd64 (11.3.0-1ubuntu1~22.04.1) ...
Removing libc6-dev:amd64 (2.35-0ubuntu3.1) ...
dpkg: warning: downgrading libc6:amd64 from 2.35-0ubuntu3.1 to 2.35-0ubuntu3
(Reading database ... 244258 files and directories currently installed.)
Preparing to unpack .../libc6_2.35-0ubuntu3_amd64.deb ...
Unpacking libc6:amd64 (2.35-0ubuntu3) over (2.35-0ubuntu3.1) ...
Setting up libc6:amd64 (2.35-0ubuntu3) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
```

### Step 2. install `openssh-server`

```
govind@thinkpad:~$ sudo apt-get install openssh-server
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  libc-dev-bin libc-devtools libcrypt-dev libnsl-dev libtirpc-dev
  linux-libc-dev rpcsvc-proto
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  gcc-12-base:i386 krb5-locales libbsd0:i386 libc6:i386 libcbor0.8:i386
  libcom-err2:i386 libcrypt1:i386 libedit2:i386 libfido2-1:i386 libgcc-s1:i386
  libgssapi-krb5-2:i386 libidn2-0:i386 libk5crypto3:i386 libkeyutils1:i386
  libkrb5-3:i386 libkrb5support0:i386 libmd0:i386 libnsl2:i386 libnss-nis:i386
  libnss-nisplus:i386 libpcre2-8-0:i386 libselinux1:i386 libssl3:i386
  libtinfo6:i386 libtirpc3:i386 libudev1:i386 libunistring2:i386 ncurses-term
  openssh-client:i386 openssh-sftp-server ssh-import-id zlib1g:i386
Suggested packages:
  glibc-doc:i386 locales:i386 krb5-doc:i386 krb5-user:i386 keychain:i386
  libpam-ssh:i386 monkeysphere:i386 ssh-askpass:i386 molly-guard monkeysphere
  ssh-askpass
The following packages will be REMOVED:
  openssh-client
The following NEW packages will be installed:
  gcc-12-base:i386 krb5-locales libbsd0:i386 libc6:i386 libcbor0.8:i386
  libcom-err2:i386 libcrypt1:i386 libedit2:i386 libfido2-1:i386 libgcc-s1:i386
  libgssapi-krb5-2:i386 libidn2-0:i386 libk5crypto3:i386 libkeyutils1:i386
  libkrb5-3:i386 libkrb5support0:i386 libmd0:i386 libnsl2:i386 libnss-nis:i386
  libnss-nisplus:i386 libpcre2-8-0:i386 libselinux1:i386 libssl3:i386
  libtinfo6:i386 libtirpc3:i386 libudev1:i386 libunistring2:i386 ncurses-term
  openssh-client:i386 openssh-server openssh-sftp-server ssh-import-id
  zlib1g:i386
0 upgraded, 33 newly installed, 1 to remove and 0 not upgraded.
Need to get 9,190 kB of archives.
After this operation, 35.8 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y

```
