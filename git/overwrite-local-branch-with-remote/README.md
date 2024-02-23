# How to overwrite your current local branch with the version from the remote repository?

## Step 1. Fetch the latest changes from the remote repository's branch
```
git fetch origin some-feature-branch
```

## Step 2. Reset your local branch to match exactly, discarding any local changes
```
git reset --hard origin/some-feature-branch
```

### Example

Consider this scenario: The team had been operating on the `main` branch (which could have been any branch) and modified the commit messages of several previous commits. Now, the situation is as follows:
  1. The remote `main` branch (i.e. `origin/main`) does not match the local `main` branch tree.
  2. You encounter an error when attempting to execute a `git pull`.
  3. You prefer not to keep your local changes.

```
govind@thinkpad:~/projects/ml-playground$ git pull
remote: Enumerating objects: 109, done.
remote: Counting objects: 100% (109/109), done.
remote: Compressing objects: 100% (109/109), done.
remote: Total 109 (delta 0), reused 109 (delta 0), pack-reused 0
Receiving objects: 100% (109/109), 21.16 KiB | 1.76 MiB/s, done.
From https://github.com/govindthange/ml-playground
 + c543f6f...9a26014 main       -> origin/main  (forced update)
 + 3464c4e...9924d7b main-test1 -> origin/main-test1  (forced update)
fatal: Not possible to fast-forward, aborting.
```

Fetch the latest changes and reset local branch (i.e. main in our example) like so:

```
govind@thinkpad:~/projects/ml-playground$ git fetch origin main
From https://github.com/govindthange/ml-playground
 * branch            main       -> FETCH_HEAD
govind@thinkpad:~/projects/ml-playground$ git reset --hard origin/main
HEAD is now at 9a26014 llm/Fix: Update file structure

```
