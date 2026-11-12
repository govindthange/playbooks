# How to configure essential Git settings like email, name, editor etc?

```bash
git config --global user.email "gthange@yahoo.com"
git config --global user.name "Govind Thange"
git config --global core.editor "vim"
git config --global core.fileMode false
git config --global alias.logg "log --pretty=format:'%C(auto,bold yellow)%h %Creset%C(auto,cyan)%ad %C(auto)%s%d %C(auto,magenta)%cr %C(auto,blue)[%an] %Creset' --graph --date=short"
```

# How do I clear false file changes after moving a repo?

Copying a repository between machines often alters file permissions, line endings, or Git LFS pointers, causing Git to flag unchanged files as modified.

## Step 1: Ignore file permission changes

```bash
git config core.fileMode false
```

## Step 2: Reset file metadata

This clears the false modification flags without altering your actual files.

```bash
git checkout -- .
```

