# How to pull all remote branches locally?

## Step 1. List all local branches (OPTIONAL)
```
govind@thinkpad:~/projects/ml-playground$ git branch
* main
```

## Step 2. List all remote branches (OPTIONAL)
```
govind@thinkpad:~/projects/ml-playground$ git branch -r
  origin/HEAD -> origin/main
  origin/main
  origin/main-poc-merge
  origin/main-rb-advsr-fix
  origin/main-test1
  origin/poc/lockr-dmnd-prdctn
  origin/poc/qna
  origin/poc/regrsn-recommit
```

## Step 3. Track all untracked remote branches

> `fetch --all` does not create local branches (which track remote branches), you have to do this manually.

Run following script to track all remote branches that aren't tracked by your local branches.
```
git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
```

## Step 4. Fetch all remote branches
```
govind@thinkpad:~/projects/ml-playground$ git fetch --all
Fetching origin
```
OR

```
git remote update
```

> `fetch` will not update local branches (which track remote branches); if you want to update your local branches you still need to pull every branch.

## Step 5. Pull all remote branches
```
govind@thinkpad:~/projects/ml-playground$ git pull --all
Fetching origin
Already up to date.
```

 
# Example

```
govind@thinkpad:~/projects/ml-playground$ git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
fatal: A branch named 'main' already exists.
Branch 'main-poc-merge' set up to track remote branch 'main-poc-merge' from 'origin'.
Branch 'main-rb-advsr-fix' set up to track remote branch 'main-rb-advsr-fix' from 'origin'.
Branch 'main-test1' set up to track remote branch 'main-test1' from 'origin'.
Branch 'poc/lockr-dmnd-prdctn' set up to track remote branch 'poc/lockr-dmnd-prdctn' from 'origin'.
Branch 'poc/qna' set up to track remote branch 'poc/qna' from 'origin'.
Branch 'poc/regrsn-recommit' set up to track remote branch 'poc/regrsn-recommit' from 'origin'.


govind@thinkpad:~/projects/poc-aiml/ml-playground$ git fetch --all
Fetching origin


govind@thinkpad:~/projects/poc-aiml/ml-playground$ git pull --all
Fetching origin
Already up to date.
```
Now, when you list branches, you will see all remote branches being tracked locally.
```
govind@thinkpad:~/projects/ml-playground$ git branch
* main
  main-poc-merge
  main-rb-advsr-fix
  main-test1
  poc/lockr-dmnd-prdctn
  poc/qna
  poc/regrsn-recommitgovind@thinkpad:~/projects/poc-aiml/ml-playground$ 
```
