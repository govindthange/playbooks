# How to manage git branches?

## Create a new branch

- git branch name1

## Rename a branch

- git branch -m Olname1dName NewName

- Rename the current branch
  - git branch -m NewName
  - git push origin  :oldname  newname

## View branches
- git branch
- git branch -r
- git branch -a
- git branch -avv
- git show-branch

## View the difference between branches
- git diff SourceBranch...TargetBranch
  -The differences are described from the point of view of target branch

## View the difference between branch logs

- git log SourceBranch...TargetBranch
  - **OR**  git shortlog SourceBranch...TargetBranch

- Use standard diff command
  - diff <(git log --oneline) <(git log branch2 --oneline)

- Alternative to using CLI for viewing difference is to use 3rd party merge tool
  - To view which merge tool is currently configured:
    - git mergetool

## View the difference between commits

- git diff HEAD~2 HEAD~1

## Merge multiple commits into one

-https://www.internalpointers.com/post/squash-commits-into-one-git

- You must timely rebase with master branch. Doing this will always put all your changes together and on top of  latest master/main branch changes. (click here)
  - git rebase master

- git rebase -i <your-7-char-commit-hash>
  - **OR**  git rebase -interactive HEAD~ <number-of-commits-to-go-back-from-AND-including-HEAD>
  - **OR**  git rebase -i HEAD~<number-of-commits-to-go-back-from-AND-including-HEAD>

## Merge Branches
- git switch to-branch-name
- git merge from-branch-name

## Delete a branch

- Delete branches that have been merged with origin/master
  -  git branch --merged origin/master | xargs git branch -d

- Delete branch locally
  - git branch -d localBranchName

- Push the deletion of your feature branch 'FeatureBranchName' to your remote git repository
    - git push --delete origin FeatureBranchName
