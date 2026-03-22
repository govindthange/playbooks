# How to effectively view committed logs in Git?

- git log

- git log BranchName

- git log -1 HEAD
  - **OR** git log --oneline --name-status -- graph
  - **OR** git log --oneline --name-status --graph --pretty="%h -  %cd - %s (%an, %ar)"
    - https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History
    - https://git-scm.com/docs/git-log
  - **OR** git log -1 --pretty=%h
  - **OR** git log -1 --pretty=%H
  - **OR** To compress commits and show only 7 characters from the SHA1
    - git log --graph --decorate --pretty=oneline --abbrev-commit

  - **OR** git log --graph

  - **OR** git log --stat

  - **OR** git log --pretty=format:"%C(auto,bold yellow)%h %Creset%C(auto,cyan)%ad %C(auto)%s%d %C(auto,magenta)%cr %C(auto,blue)[%an] %Creset" --graph --date=short
  - **OR** git log --pretty=format:"%C(auto,yellow)%h %C(auto,magenta)% G? %C(auto,cyan)%>(18,trunc)%ad %C(auto, bold red)%gD%D %C(auto,reset)%s - %C(auto,green)%<(8,trunc)%aN %C(bold blue)(%cr)%Creset"  --graph --date=short

- git show CommitHashAsSeenInTheAboveLogOutput

- git diff HEAD~2 HEAD~1


# How to create an alias for quickly viewing committed logs?

## Step 1. Create a global alias.

```console
git config --global alias.logg "log --pretty=format:'%C(auto,bold yellow)%h %Creset%C(auto,cyan)%ad %C(auto)%s%d %C(auto,magenta)%cr %C(auto,blue)[%an] %Creset' --graph --date=short"
```

## Step 2. Use the created alias.

```console
govind@thinkpad:~/projects/de-chess$ git logg
* 4c25e75 2022-12-01 Add vote-referendum functionality (HEAD -> feature/85-governance-gpl, origin/feature/85-governance-gpl) 3 years, 4 months ago [Govind Thange] 
* ae8825d 2022-12-01 Add electorate & referendum contract 3 years, 4 months ago [Govind Thange] 
*   53a5bb0 2022-11-27 Add crowdsale component (origin/feature/85-governance, feature/85-governance) 3 years, 4 months ago [Govind Thange] 
|\  
| * 447dcd1 2022-11-20 Add contracts for governance (origin/feature/86) 3 years, 4 months ago [Govind Thange] 
* | a87a1e0 2022-11-27 Add UI templates for high level governance modules 3 years, 4 months ago [Govind Thange] 
* | 9e380ac 2022-11-20 Add contracts for governance 3 years, 4 months ago [Govind Thange] 
* | 2a04998 2022-11-26 Show k8s cluster in architecture diagram (origin/dev, dev) 3 years, 4 months ago [Govind Thange] 
|/  
*   7d0c0c7 2022-11-20 Merge branch 'feature/84-crowdfunding' into dev 3 years, 4 months ago [Govind Thange] 
|\  
| * fa1ae12 2022-11-13 Implement crowdfunding contract for chess tournaments #84 (origin/feature/84-crowdfunding, feature/84-crowdfunding) 3 years, 4 months ago [Govind Thange] 
* | 71e9bc0 2022-11-20 Explain system and its governance model w/ architecture diagram #1 3 years, 4 months ago [Govind Thange] 
|/  
* a1e3bb3 2022-11-12 Implement scan-to-play feature #83 (origin/feature/83-scan-to-play, feature/83-scan-to-play) 3 years, 4 months ago [Govind Thange] 
* dc909a8 2022-11-12 Apply Bulma CSS to swap feature components #69 (origin/feature/69-swap-with-bulma, feature/69-swap-with-bulma) 3 years, 4 months ago [Govind Thange] 
* d9c5e5e 2022-11-12 Stop tracking contract ABIs in git #81 (origin/feature/73-nft-mint, feature/73-nft-mint) 3 years, 4 months ago [Govind Thange] 
* 06f9d15 2022-11-12 Implement full client-to-dapp flow for minting NFT #76 3 years, 4 months ago [Govind Thange] 
* d9bac49 2022-11-12 Optimize Web3Util.js and add page to test contracts #78 3 years, 4 months ago [Govind Thange] 
* e2c03fe 2022-11-12 Implement client side code to upload NFT images to server #75 3 years, 4 months ago [Govind Thange] 
```
