# How to fix author name and/or email IDs in commit history?

## Step 1. Clone as bare repository

Create a temporary folder and clone the current repository as a bare one like so:

```
govind@thinkpad:~/temp$ git clone --bare https://github.com/govindthange/devops.git
Cloning into bare repository 'devops.git'...
remote: Enumerating objects: 165, done.
remote: Counting objects: 100% (165/165), done.
remote: Compressing objects: 100% (115/115), done.
remote: Total 165 (delta 62), reused 144 (delta 41), pack-reused 0
Receiving objects: 100% (165/165), 84.70 KiB | 2.82 MiB/s, done.
Resolving deltas: 100% (62/62), done.
```

## Step 2. Verify bare repo content

```
govind@thinkpad:~/temp$ cd devops.git/
govind@thinkpad:~/temp/devops.git$ ll
total 44
drwxrwxr-x  7 govind govind 4096 Aug  7 10:15 ./
drwxrwxr-x 11 govind govind 4096 Aug  7 10:15 ../
drwxrwxr-x  2 govind govind 4096 Aug  7 10:15 branches/
-rw-rw-r--  1 govind govind  134 Aug  7 10:15 config
-rw-rw-r--  1 govind govind   73 Aug  7 10:15 description
-rw-rw-r--  1 govind govind   21 Aug  7 10:15 HEAD
drwxrwxr-x  2 govind govind 4096 Aug  7 10:15 hooks/
drwxrwxr-x  2 govind govind 4096 Aug  7 10:15 info/
drwxrwxr-x  4 govind govind 4096 Aug  7 10:15 objects/
-rw-rw-r--  1 govind govind  103 Aug  7 10:15 packed-refs
drwxrwxr-x  4 govind govind 4096 Aug  7 10:15 refs/
```

## Step 3. Modify the rename-author.sh

1. Open `./rename-author.sh`
2. Change `OLD_EMAIL` variable to match the incorrect email ID that you want to fix.
3. Similarly modify `CORRECT_NAME` and `CORRECT_EMAIL` to the name and email ID you want to change to.

## Step 4. Run the rename-author.sh

```
govind@thinkpad:~/temp/devops.git$ ~/projects/playbooks/git/fix-author-in-commit-history/rename-author.sh 
WARNING: git-filter-branch has a glut of gotchas generating mangled history
	 rewrites.  Hit Ctrl-C before proceeding to abort, then use an
	 alternative filtering tool such as 'git filter-repo'
	 (https://github.com/newren/git-filter-repo/) instead.  See the
	 filter-branch manual page for more details; to squelch this warning,
	 set FILTER_BRANCH_SQUELCH_WARNING=1.
Proceeding with filter-branch...

Rewrite 8bccd43ed86b098c845258d64b14f690821f5098 (23/23) (0 seconds passed, remaining 0 predicted)    
Ref 'refs/heads/main' was rewritten
```

## Step 5. Verify author name in the log

```
govind@thinkpad:~/temp/devops.git$ git log
commit 24d0009e9812c290fc533e9e337ea8c14e1b00d7 (HEAD -> main)
Author: Govind Thange <gthange@yahoo.com>
Date:   Wed Jul 12 17:36:14 2023 +0530
...
```

## Step 6. Push repository to remote

```
govind@thinkpad:~/temp/devops.git$ git push --force --tags origin 'refs/heads/*'
Enumerating objects: 112, done.
Counting objects: 100% (112/112), done.
Delta compression using up to 16 threads
Compressing objects: 100% (55/55), done.
Writing objects: 100% (105/105), 23.88 KiB | 23.88 MiB/s, done.
Total 105 (delta 44), reused 86 (delta 43), pack-reused 0
remote: Resolving deltas: 100% (44/44), completed with 3 local objects.
To https://github.com/govindthange/devops.git
 + 8bccd43...24d0009 main -> main (forced update)
govind@thinkpad:~/temp/devops.git$ 
```

Delete the local git directory like so:

```
govind@thinkpad:~/temp/devops.git$ cd ../
govind@thinkpad:~/temp$ rm --rf devops.git
rm: unrecognized option '--rf'
Try 'rm --help' for more information.
govind@thinkpad:~/temp$ rm -rf devops.git
govind@thinkpad:~/temp$ 

```

## Step 7. Pull from remote repository

### 1. Go to your local repository

### 2. Backup Your Changes (Optional)

If you have made any local changes that you want to keep, make sure to create a backup or copy of your repository folder before proceeding.

### 3. Switch to main branch

Make sure you are on the branch you want to reset (e.g., main)

```
git checkout main
```

### 4. Reset Your Local Branch

Use the following commands to reset your local branch to the state of the remote branch:

```
govind@thinkpad:~/projects/devops$ git reset --hard origin/main
HEAD is now at 24d0009 Rename approval flow script w/ hardcoded emails
```

### 5. Pull Remote Changes

Pull the latest changes from the remote repository to ensure your local branch is up to date:

```
govind@thinkpad:~/projects/devops$ git pull origin main
From https://github.com/govindthange/devops
 * branch            main       -> FETCH_HEAD
Already up to date.

```

## Step 6. Check and update git config

### 1. Check user.name/email

```
govind@thinkpad:~/projects/devops$ git config --global user.name
Govind Thange
govind@thinkpad:~/projects/devops$ git config --global user.email
govindt@XYZ.com
govind@thinkpad:~/projects/devops$ 

```

### 2. Update user.name/email

Use following command to update user.name/email

-  git config [--system | --global | --local] user.name "Govind Thange"
-  git config [--system | --global | --local] user.email "gthange@yahoo.com"

```
govind@thinkpad:~/projects/devops$ git config --global user.name "Govind Thange"
govind@thinkpad:~/projects/devops$ git config --global user.email "gthange@yahoo.com"
govind@thinkpad:~/projects/devops$ git config --global user.name
Govind Thange
govind@thinkpad:~/projects/devops$ git config --global user.email
gthange@yahoo.com
```