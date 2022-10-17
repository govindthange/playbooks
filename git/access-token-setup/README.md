# How to setup GitHub access token for development?

## Step 1. Create an access token.

1. Go to `https://github.com/settings/tokens` page.
2. Click on `Personal access tokens` tab on the left side.
3. On `Personal access tokens` page hit `Generate new token` button.
4. Specify `Note` and `Expiration` for the new token.
5. Check `repo` in the `Select scopes` list. Ignore rest for development purpose.
6. Hit `Generate token` button to create a new token.
7. Github will generate a new token string and display it.
8. Copy this token string and save it.

## Step 2. Configure git on your dev machine.

1. Open command line terminal on your dev machine.
2. Go to your project folder.
3. Use `git config` command to configure credentials and persist it across sessions.
  ```
  govind@thinkpad:~/projects/kit$ git config --global user.name "Govind Thange"
  govind@thinkpad:~/projects/kit$ git config --global user.email "gthange@yahoo.com"
  govind@thinkpad:~/projects/kit$ git config --global credential.helper cache
  ```
4. Verify configuration using `git config -l`.
  ```
  govind@thinkpad:~/projects/kit$ git config -l
  user.name=Govind Thange
  user.email=gthange@yahoo.com
  core.editor=vim
  credential.helper=cache
  ```
  
  ## Step 3. Interact w/ your repository.
  
  Try `git pull` to interact w/ remote repository and enter username and password (i.e. your access-token) when prompted.
  ```
  govind@thinkpad:~/projects/kit/ethereum-truffle-kit$ git pull
  Username for 'https://github.com': govindthange
  Password for 'https://govindthange@github.com': 
  Already up to date.
  ```
  
  ### Finally unset the access-token when not required.
  
  Use `git config --unset` command to clear access-token from cache.
  ```
  govind@thinkpad:~/projects/kit$ git config --global --unset credential.helper 
  ```
  
