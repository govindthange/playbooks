# Stash Changes

Before switching to any new work first pause and save the on going work using `git stash`.

- git stash
- **OR**  git stash save SomeStashName
- **OR**  git stash push NameOfTheFile.ext
- **OR**  git stash push NameOfTheFile.ext --patch

You may view a list of all your stashes like so:

- git stash list

To view a particular stashed change do like so:

- git stash show TheStashIndexNumber
- **OR**  git stash show stash@{TheStashIndexNumber}

You may view all the changes in a given stash like so:

- git stash show TheStashIndexNumber -p
- **OR**  git stash show stash@{TheStashIndexNumber} -p

## Apply stashed changes

Apply stashed changes back to worksapce like so:
- git stash apply
- git stash apply TheStashIndexNumber

## Delete stashed changes

Delete a stash like so:
- git stash drop TheStashIndexNumber

Delete all stashed changes like so:
- git stash clear

#### How to apply multiple stashed changes?
[...](https://newbedev.com/how-to-combine-multiple-stashes-in-git)

First pop one stash, commit it, and then pop and work with the next one.
- $ git stash pop
- $ git add . && git commit -am 'WIP'
- $ git stash pop

You can even do as follows. Note that stash@{3} would become stash@{2} since the first one was popped off.
- git stash apply stash@{1}
- git commit -am "WIP"
- git stash apply stash@{3}

A better way is to just use following:
- git stash show -p stash@{whatever} > stash-{whatever}.diff
- Then use following for each one:
 - git apply

To undo what you popped
- $ git reset --soft HEAD^

---

# Stage Changes: Prepare for a commit

Stage you changes before performing any commit.

Stage all changes like so:
- git add .

Stage a single file like so:
- git add FileName.ext

Stage one ore more snippets from a single/multiple files like so:

First, indicate indicate that you want to add a small patch from ongoing set of changes like so:
- git add --patch .
- **OR**  git add --patch FileName.ext

Next, follow Git's command line workflow to specify those changes.
- (1/TotalHunks) Stage this hunk [y,n,q,a,d,e,?]? help

Tip: [...](https://medium.com/transmute-techtalk/improve-your-commit-hygiene-with-git-add-patch-3b7dd9c117c4)

---

# Revert Changes: Unstage, Undo or clean all

Remove staged files, which you staged using "git add FileName", w/o touching the untracked files or files that were unstaged.

- git restore --staged .
- **OR**  git restore --staged FileName.ext

Remove a committed file from repository **but keep it locally** like so:

- git rm --cached FileName.ext

Unstage by removing files from the index in a more meaningful way

- git reset
- git reset HEAD -- FileName
- git reset HEAD .

Undo the last ongoing commit

- git reset --soft HEAD~1

Clean everything i.e., removing all untracked files as well as all staged files. 1. git reset --hard HEAD
    1. git reset --hard HEAD~1
    2. Revert everything and reset to whats in the remote Branch (master, x..)
      - git reset --hard origin/BranchName

---

# Commit Changes

Once all your changes have been staged, you can proceed with commit operation.

Glance at the summary of changes like so:
- git status

Analyze staged changes with last commit like so:
- git diff --cached HEAD^
- git diff --cached
- git diff --cached HEAD~1

Analyze last 2 committed changes like so:
- git diff HEAD~2 HEAD~1

When ready, commit in two steps like so:
- git add "FileName.ext"
- git commit -m "PutYourCommentsHere"

**OR** you may also commit in a single step (not advisable!)
- git commit -am "add-n-commit in single step"

Note: The `-m` flag commits immediately. By adding `-e`, Git will take the text in the quotes, load it into your text editor, and allow you to expand on it or fix typos before you save and exit to finish the commit.

- git commit -e

Fix previous commit message like so:

- git commit --ammend -m "modified message"

---

# Push Changes

Check your remote repository

- git remote -v
- **OR**  git remote --verbose

Push locally committed changes to remote like so:

- git push -u origin BranchName

The `-u` option tells Git to track the remote branch i.e. Git should tie your local branch with the remote branch and keep them in sync.

**Note:** Sync does not happen automatically. You have to use fetch command to do so.

---

# Track Remote Changes

See how repository is configured and tracking like so:

- git remote show origin
 
Although here local branch ‘dev’ is tracking the remote branch with the same name ‘dev’; it is not mandatory. Local branch can track a remote branch with a different name. Ex. a local ‘foo’ branch can track a remote branch ‘bar’.

Download the difference from the remote and see w/o applying them like so:

1. git fetch
2. git status
3. Now pull the changes from the remote (pulling = fetching + mergin)

---

# Pull Remote Changes

1. git pull origin BranchName
    - [git pull origin BranchName] = [git fetch] + [git merge]
2. To take updates from the original main forefather repository
    - Add an upstream remote like so:
        - git remote add upstream https://github.com/<original-owner>/<originalrepository>.git
3. Update local repo directly with any changes from the central repo
    1. Get the central repository URL (Ref: https://github.com/trilloapps/cloud.git)
    2. Add the remote
        - git remote add upstream
            - Ref: https://github.com/trilloapps/cloud.git
    3. Update local repository
        1. git pull upstream master
        2. git push origin master

---

# How to analyze changes in a file?

1. git blame FileName.ext
    - **OR**  git gui blame FileName.ext
    - **OR**  git blame -M -C --color-lines --color-by-age FileName.ext
2. git show CommitHashAsSeenInTheAboveBlameOutput
3. git diff HEAD~2 HEAD~1

# How to search for changes in a repository?

To search "war" by ignoring the case across all folders do as follows:

- git grep -w -i "war"
