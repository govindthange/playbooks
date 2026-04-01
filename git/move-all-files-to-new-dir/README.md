# How to move all files from current folder to new folder in GIT?

## Step 1. Create the folder if you haven't already.

```shell
mkdir -p my-new-subfolder
```

## Step 2. Move everything to the new folder.

Run following code:

```shell
# This moves everything (including hidden files) into the subfolder
# while ignoring the subfolder itself and the .git directory
for file in $(ls -A | grep -v 'my-new-subfolder' | grep -v '.git'); do
    git mv "$file" my-new-subfolder/
done
```

## Step 3. Move all hidden files to the new folder.

Run following code:

```shell
# Move everything except the .git folder and the 'market' folder itself
find . -maxdepth 1 ! -name "." ! -name ".git" ! -name "my-new-subfolder" -exec git mv {} market/ \;
```
