# How to overwrite your current local branch with the version from the remote repository?

## Step 1. Fetch the latest changes from the remote repository's branch
```
git fetch origin some-feature-branch
```

## Step 2. Reset your local branch to match exactly, discarding any local changes
```
git reset --hard origin/some-feature-branch
```
