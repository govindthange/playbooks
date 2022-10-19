# How to add tag?

## Step 1. Add tag w/ description.

```
govind@thinkpad:~/de-chess$ git tag -a "workspace" -m "The base workspace pulled from https://github.com/govindthange/kit"
```

## Step 2. Push tag to remote branch.

If your local branch is on `master` and it tracks `main` branch in remote then do as follows:

```
govind@thinkpad:~/de-chess$ git push origin HEAD:main --tags
```
