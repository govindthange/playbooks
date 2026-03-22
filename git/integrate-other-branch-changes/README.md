# How to integrate changes from other branches?

Changes from other branch can be committed fully or partially in following ways:

## Scenario 1: Merge **a single fix** from other branch

Merge a complete fix from another branch using a **hash** or **branch name** along with a number (i.e. the amount of commits since HEAD) like so:

- git cherry-pick CommitHashOfTheFix
- git cherry-pick BranchName~1

## Scenario 2: Merge **a set of fixes** from other branch

Merge a set of complete fixes from other branch (say feat1) like so:

- git cherry-pick feat1~2..feat1~0


## Scenario 3. Merge **a part of feature/fix** from other branch.

Merge a part of committed feature or fix like so:

### **Step 1.** Create a patch file.

- git format-patch -1 CommitHashOfTheFix
    - `-1` is the amount of commits since the given hash (CommitHashOfTheFix)
    - You can also use `HEAD` instead of the hash to take from the latest version

This will create SomeNumber-HyphenedCommitMessage.patch file in the root folder

### **Step 2.** Now switch to the branch you want to apply this commit to.

### **Step 3.** Apply the patch like so:

- git apply SomeNumber-HyphenedCommitMessage.patch
