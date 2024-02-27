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

### Track branches in remote `origin`
```
git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
```

### Track branches in remote `origin2`
```
git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin2/}" "$remote"; done
```

## Step 4. Fetch all remote branches
```
git remote update
```

OR

```
govind@thinkpad:~/projects/ml-playground$ git fetch --all
Fetching origin
remote: Enumerating objects: 23, done.
remote: Counting objects: 100% (16/16), done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 23 (delta 6), reused 15 (delta 5), pack-reused 7
Unpacking objects: 100% (23/23), 618.83 KiB | 8.84 MiB/s, done.
From https://github.com/govindthange/ml-playground
   9a26014..8a1b086  main                  -> origin/main
 * [new branch]      main-rb-advsr-fix     -> origin/main-rb-advsr-fix
 * [new branch]      poc/lockr-dmnd-prdctn -> origin/poc/lockr-dmnd-prdctn
Fetching origin2
Username for 'http://192.9.200.47:8080': govindt
Password for 'http://govindt@192.9.200.47:8080': 
From http://192.9.200.47:8080/git/root/poc-aiml
 * [new branch]      main                  -> origin2/main
 * [new branch]      main-poc-merge        -> origin2/main-poc-merge
 * [new branch]      main-rb-advsr-fix     -> origin2/main-rb-advsr-fix
 * [new branch]      main-test1            -> origin2/main-test1
 * [new branch]      poc/lockr-dmnd-prdctn -> origin2/poc/lockr-dmnd-prdctn
 * [new branch]      poc/qna               -> origin2/poc/qna
 * [new branch]      poc/regrsn-recommit   -> origin2/poc/regrsn-recommit
```

> `fetch` will not update local branches (which track remote branches); if you want to update your local branches you still need to pull every branch.

## Step 5. Pull all remote branches
```
govind@thinkpad:~/projects/ml-playground$ git pull --all
Fetching origin
Fetching origin2
Username for 'http://192.9.200.47:8080': govindt
Password for 'http://govindt@192.9.200.47:8080': 
Updating 9a26014..8a1b086
Fast-forward
 llm/robo-advisor/data_processor.py | 15 ++++++++-------
 llm/robo-advisor/robo_advisor.py   | 80 ++++++++++++++++++++++++++++++++++++++++++++++++++++++--------------------------
 2 files changed, 62 insertions(+), 33 deletions(-)

```

---

### Example

#### Track branches in remote `origin`
```
govind@thinkpad:~/projects/ml-playground$ git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
fatal: A branch named 'main' already exists.
Branch 'main-poc-merge' set up to track remote branch 'main-poc-merge' from 'origin'.
Branch 'main-rb-advsr-fix' set up to track remote branch 'main-rb-advsr-fix' from 'origin'.
Branch 'main-test1' set up to track remote branch 'main-test1' from 'origin'.
Branch 'poc/lockr-dmnd-prdctn' set up to track remote branch 'poc/lockr-dmnd-prdctn' from 'origin'.
Branch 'poc/qna' set up to track remote branch 'poc/qna' from 'origin'.
Branch 'poc/regrsn-recommit' set up to track remote branch 'poc/regrsn-recommit' from 'origin'.
```

#### Track branches in remote `origin2`
```
govind@thinkpad:~/projects/ml-playground$ git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin2/}" "$remote"; done
Branch 'origin/main' set up to track remote branch 'main' from 'origin'.
Branch 'origin/main-poc-merge' set up to track remote branch 'main-poc-merge' from 'origin'.
Branch 'origin/main-test1' set up to track remote branch 'main-test1' from 'origin'.
Branch 'origin/poc/clustrng' set up to track remote branch 'poc/clustrng' from 'origin'.
Branch 'origin/poc/qna' set up to track remote branch 'poc/qna' from 'origin'.
Branch 'origin/poc/regrsn' set up to track remote branch 'poc/regrsn' from 'origin'.
Branch 'origin/poc/regrsn-recommit' set up to track remote branch 'poc/regrsn-recommit' from 'origin'.
Branch 'origin/poc/robo-advisor' set up to track remote branch 'poc/robo-advisor' from 'origin'.
Branch 'origin/poc/robo-advisor-recommit' set up to track remote branch 'poc/robo-advisor-recommit' from 'origin'.
```

#### Fetch all branches
```
govind@thinkpad:~/projects/ml-playground$ git fetch --all
Fetching origin
Fetching origin2
Username for 'http://192.9.200.47:8080': govindt 
Password for 'http://govindt@192.9.200.47:8080': 
```

#### Pull all branches
```
govind@thinkpad:~/projects/ml-playground$ git pull --all
Fetching origin
Fetching origin2
Username for 'http://192.9.200.47:8080': govindt
Password for 'http://govindt@192.9.200.47:8080': 
```

Now, when you list branches, you will see all branches from `multiple remotes` being tracked locally.
```
govind@thinkpad:~/projects/ml-playground$ git branch -r
  origin/HEAD -> remotes/origin/main
  origin/main
  origin/main-poc-merge
  origin/main-rb-advsr-fix
  origin/main-test1
  origin/poc/clustrng
  origin/poc/lockr-dmnd-prdctn
  origin/poc/qna
  origin/poc/regrsn
  origin/poc/regrsn-recommit
  origin/poc/robo-advisor
  origin/poc/robo-advisor-recommit
  origin2/main
  origin2/main-poc-merge
  origin2/main-rb-advsr-fix
  origin2/main-test1
  origin2/poc/lockr-dmnd-prdctn
  origin2/poc/qna
  origin2/poc/regrsn-recommit
```
