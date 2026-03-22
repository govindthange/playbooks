# How to setup SSH Keys for authentication?

> Git Password Retirement wall: Since 2021, GitHub has required **Personal Access Tokens (PATs)** or SSH keys instead of your account password for command-line operations.

SSH Keys is the "Set it and Forget it" solution. It is the professional standard because one never has to type a password or token again.

## Step 1. Check for keys.
See if you have one already: `ls -al ~/.ssh`.

If the key exists, jump to step 3.

```console
govind@thinkpad:~$ ls -al ~/.ssh
total 36
drwx------  2 govind govind 4096 Mar 22 15:52 .
drwxr-xr-x 49 govind govind 4096 Mar 22 16:01 ..
-rw-r--r--  1 root   root     27 Mar  6  2022 environment
-rw-------  1 govind govind  411 Mar 22 15:56 id_ed25519
-rw-r--r--  1 govind govind   99 Mar 22 15:56 id_ed25519.pub
-rw-------  1 govind govind 2610 Aug 15  2021 id_rsa
-rw-r--r--  1 govind govind  573 Aug 15  2021 id_rsa.pub
-rw-------  1 govind govind 1992 May 29  2023 known_hosts
-rw-r--r--  1 govind govind 2212 Aug 30  2022 known_hosts.old
```

## Step 2. Generate a new key (if there are no keys!)
Run the following command.
```console
ssh-keygen -t ed25519 -C "gthange@yahoo.com"
```

## Step 3. Add the key to GitHub
1. Copy the contents of `~/.ssh/id_ed25519.pub`
2. Go to **Settings** > **SSH and GPG keys**
3. Click on **New SSH Key** button.
4. Paste the content of `id_ed25510.pub` file inside `key` text area.
5. Hit **Add SSH Key** button to save.

## Step 4. Switch your URL.
Change your repo from HTTPS to SSH like so:

```console
git remote set-url origin git@github.com:govindthange/kit.git`
```
