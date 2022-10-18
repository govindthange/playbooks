# How to move a sub-directory in an existing respository to a new repository w/ history?

Lets say we want to copy `kit/ethereum-truffle-kit` folder inside `https://github.com/govindthange/kit` respository into a new repository `de-chess` without losing its history.

## Step 1. Create a new repository.

We create a new repository by the name `de-chess`.

## Step 2. Clone the existing repository in your dev machine.

We clone the repository that has the folder we want to move into a new repository w/ history.

```
govind@thinkpad:~$ git clone https://github.com/govindthange/kit.git
Cloning into 'kit'...
remote: Enumerating objects: 823, done.
remote: Counting objects: 100% (474/474), done.
remote: Compressing objects: 100% (331/331), done.
remote: Total 823 (delta 215), reused 344 (delta 127), pack-reused 349
Receiving objects: 100% (823/823), 1.03 MiB | 930.00 KiB/s, done.
Resolving deltas: 100% (340/340), done.
```

## Step 3. Remove the remote reference.
  
We remove the remote reference to our git repository so that we don't accidentally push these changes.
  
 ```
govind@thinkpad:~/kit$ git remote show 
origin
govind@thinkpad:~/kit$ git remote rm origin
govind@thinkpad:~/kit$ git remote show
govind@thinkpad:~/kit$ 
 ```

## Step 4. Use filter-branch to rewrite master.

Note the content of the directory.

```
govind@thinkpad:~/kit$ ls -ltr
total 40
-rw-rw-r-- 1 govind govind  312 Oct 18 19:50 README.md
drwxrwxr-x 3 govind govind 4096 Oct 18 19:50 ansible-kit
drwxrwxr-x 7 govind govind 4096 Oct 18 19:50 ethereum-kit
drwxrwxr-x 3 govind govind 4096 Oct 18 19:50 k8s-helm-kit
drwxrwxr-x 4 govind govind 4096 Oct 18 19:50 jenkins-kit
drwxrwxr-x 2 govind govind 4096 Oct 18 19:50 hardhat-kit
drwxrwxr-x 7 govind govind 4096 Oct 18 19:50 ethereum-truffle-kit
drwxrwxr-x 3 govind govind 4096 Oct 18 19:50 networking-kit
drwxrwxr-x 5 govind govind 4096 Oct 18 19:50 vscode-container-kit
drwxrwxr-x 2 govind govind 4096 Oct 18 19:50 ux-kit
```

Now use `filter-branch` to rewrite master with `ethereum-truffle-kit` directory. This is the directory we intended to move into a new repository while preserving its history.

```
govind@thinkpad:~/kit$ git filter-branch --subdirectory-filter ethereum-truffle-kit -- --all
WARNING: git-filter-branch has a glut of gotchas generating mangled history
	 rewrites.  Hit Ctrl-C before proceeding to abort, then use an
	 alternative filtering tool such as 'git filter-repo'
	 (https://github.com/newren/git-filter-repo/) instead.  See the
	 filter-branch manual page for more details; to squelch this warning,
	 set FILTER_BRANCH_SQUELCH_WARNING=1.
Proceeding with filter-branch...

Rewrite 7df3df390db6aa677a24d570be9d4e943da89fb2 (15/23) (1 seconds passed, remaining 0 predicted)    
Ref 'refs/heads/master' was rewritten
```

Note that everything has been replaced by the content of `ethereum-truffle-kit`.

```
govind@thinkpad:~/kit$ ls -ltr
total 44
-rw-rw-r-- 1 govind govind 1794 Oct 18 19:59 README.md
-rwxrwxr-x 1 govind govind  136 Oct 18 19:59 clean.sh
drwxrwxr-x 2 govind govind 4096 Oct 18 19:59 blockchain
drwxrwxr-x 3 govind govind 4096 Oct 18 19:59 app
drwxrwxr-x 6 govind govind 4096 Oct 18 19:59 client
drwxrwxr-x 4 govind govind 4096 Oct 18 19:59 ipfs
-rw-rw-r-- 1 govind govind 2056 Oct 18 19:59 docker-compose.yml
-rw-rw-r-- 1 govind govind  317 Oct 18 19:59 docker-compose-dapp.yml
-rw-rw-r-- 1 govind govind  346 Oct 18 19:59 docker-compose-client.yml
-rw-rw-r-- 1 govind govind  241 Oct 18 19:59 docker-compose-app.yml
drwxrwxr-x 8 govind govind 4096 Oct 18 19:59 dapp
```

## Step 5. Create a new directory for our new repository.

Create a new directory by the name `de-chess` and move all `kit` directory contents into it.

```
govind@thinkpad:~/kit$ mkdir ~/de-chess
govind@thinkpad:~/kit$ mv * ~/de-chess
govind@thinkpad:~/kit$ mv .git ~/de-chess/.git
govind@thinkpad:~/kit$ mv .env ~/de-chess/
```

Ensure that all hidden files & folder are also moved.

```
govind@thinkpad:~/kit$ ls -ltra
total 8
drwxr-xr-x 46 govind govind 4096 Oct 18 20:09 ..
drwxrwxr-x  2 govind govind 4096 Oct 18 20:11 .
```

## Step 6. Add new repository as remote.

Add remote referencing to the new repository we created in Step #1 above.

```
govind@thinkpad:~/de-chess$ git remote add origin https://github.com/govindthange/de-chess.git
govind@thinkpad:~/de-chess$ git remote show
origin
govind@thinkpad:~/de-chess$ git remote -v
origin	https://github.com/govindthange/de-chess.git (fetch)
origin	https://github.com/govindthange/de-chess.git (push)
```

## Step 7. Merge local repository w/ the remote repository.

### Step 7.1. Base your work on the upstream.

In order to base your work on an existing remote upstream branch run `git fetch` to retrieve it.

```
govind@thinkpad:~/de-chess$ git fetch
warning: no common commits
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 12.39 KiB | 704.00 KiB/s, done.
From https://github.com/govindthange/de-chess
 * [new branch]      main       -> origin/main

```

### Step 7.2. Confirm the remote main branch has been retreived.

```
govind@thinkpad:~/de-chess$ git branch -a
* master
  remotes/origin/main
```

### Step 7.3. Make local branch 'master' to track the remote branch 'main'.

```
govind@thinkpad:~/de-chess$ git branch --set-upstream-to=remotes/origin/main master
Branch 'master' set up to track remote branch 'main' from 'origin'.
```

### Step 7.4. Pull updates from the new remote repository.

> Note that if you try to do `git pull` the git will refuse to merge the changes because they are unrelated.

```
govind@thinkpad:~/de-chess$ git pull origin main
From https://github.com/govindthange/de-chess
 * branch            main       -> FETCH_HEAD
fatal: refusing to merge unrelated histories
```

Use `allow-unrelated-histories` flag and then run `git pull`.

```
govind@thinkpad:~/de-chess$ git pull origin main --allow-unrelated-histories
From https://github.com/govindthange/de-chess
 * branch            main       -> FETCH_HEAD
Merge made by the 'recursive' strategy.
 LICENSE | 674 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 674 insertions(+)
 create mode 100644 LICENSE
govind@thinkpad:~/de-chess$ 
```

### Step 7.5. Push changes to the new remote repository.

```
govind@thinkpad:~/de-chess$ git push origin HEAD:main
Enumerating objects: 287, done.
Counting objects: 100% (287/287), done.
Delta compression using up to 12 threads
Compressing objects: 100% (167/167), done.
Writing objects: 100% (286/286), 423.72 KiB | 141.24 MiB/s, done.
Total 286 (delta 120), reused 245 (delta 110)
remote: Resolving deltas: 100% (120/120), done.
To https://github.com/govindthange/de-chess.git
   37a2d0f..16773c5  HEAD -> main
Branch 'master' set up to track remote branch 'main' from 'origin'.
```
