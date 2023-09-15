
# Ubuntu shows all files and foldres of Home directory, how to show clean desktop on ubuntu?

Check if the Desktop Directory Exists:

## Step 1. First verify whether the Desktop directory exists in home directory

Use ls command to see Desktop directory listing.

```
ls ~/Desktop
```

Use the ls -l command to view the symbolic link's target path.
 
```
ls -l /home/govind/Desktop
```

## Step 2. Remove and recreate the Desktop directory

``` 
rm /home/govind/Desktop
mkdir ~/Desktop
cd ~/Desktop
```
If the permissions are not correct, you can adjust them using the chmod command:

```
chmod 755 ~/Desktop
```

## Step 3. Fix user-dirs.dirs file

Look in `~/.config/user-dirs.dirs` and modify `XDG_DESKTOP_DIR` value.

```
vi ~/.config/user-dirs.dirs
```

Change `XDG_DESKTOP_DIR` value like so:

```
XDG_DESKTOP_DIR="$HOME/Desktop"
```
