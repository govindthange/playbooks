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
  
  ## Step 3. Use access token created in Step #1 above.
  
  Try `git pull` to interact w/ remote repository and enter `username` and `personal-access-tokenpassword` (instead of the password) when prompted.
  ```
  govind@thinkpad:~/projects/kit/ethereum-truffle-kit$ git pull
  Username for 'https://github.com': govindthange
  Password for 'https://govindthange@github.com': < ENTER YOUR PERSONAL ACCESS TOKEN HERE >
  Already up to date.
  ```
  
  ### Finally unset the access-token when not required.
  
  Use `git config --unset` command to clear access-token from cache.
  ```
  govind@thinkpad:~/projects/kit$ git config --global --unset credential.helper 
  ```
  
---

# FAQ

## Q: How to create personal access tokens (PAT)?

Personal access tokens (PATs) provide a secure alternative to passwords for Docker CLI authentication. Use PATs to authenticate automated systems, CI/CD pipelines, and development tools without exposing your Docker Hub password.

> Treat access tokens like passwords and keep them secure. Store tokens in credential managers and never commit them to source code repositories.

To create a personal access token:

1. Sign in to [Docker Home](https://app.docker.com/).
1. Select your avatar in the top-right corner and from the drop-down menu select **Account settings**.
1. Select **Personal access tokens**.
1. Select **Generate new token**.
1. Configure your token:
   - **Description:** Use a descriptive name that indicates the token's purpose
   - **Expiration date:** Set an expiration date based on your security policies
   - **Access permissions:** **Read**, **Write**, or **Delete**.
1. Select **Generate**. Copy the token that appears on the screen and save it. You won't be able to retrieve the token once you exit the screen.

## Q: How to use personal access tokens?

Sign in to the Docker CLI using your personal access token:

```console
$ docker login --username <YOUR_USERNAME>
Password: [paste your PAT here]
```

When prompted for a password, enter your personal access token instead of your Docker Hub password.

#### Modify personal access tokens

> [!NOTE]
>
> You can't edit the expiration date on an existing personal access token. You must create a new PAT if you need to set a new expiration date.

You can rename, activate, deactivate, or delete a token as needed. You can manage your tokens in your account settings.

1. Sign in to [Docker Home](https://app.docker.com/login).
1. Select your avatar in the top-right corner and from the drop-down menu select **Account settings**.
1. Select **Personal access tokens**.
      - This page shows an overview of all your tokens, and lists if the token was generated manually or if it was
   [auto-generated](#auto-generated-tokens). You can also view the scope of the
   tokens, which tokens are activate and inactive, when they were created, when
   they were last used, and their expiration date.
1. Select the actions menu on the far right of a token row, then select **Deactivate** or **Activate**, **Edit**, or **Delete** to modify the token.
1. After editing the token, select **Save token**.

#### Auto-generated tokens

Docker Desktop automatically creates authentication tokens when you sign in, with these characteristics:

- Automatic creation: Generated when you sign in to Docker Desktop
- Full permissions: Include Read, Write, and Delete access
- Session-based: Automatically removed when Docker Desktop session expires
- Account limits: Up to 5 auto-generated tokens per account
- Automatic cleanup: Older tokens are deleted when new ones are created

You can manually delete auto-generated tokens if needed, but they'll be recreated when you use Docker Desktop.

## Q: How to fix `error storing credentials`?

```bash
What is this error:
govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker login -u thange
Password: 
Error saving credentials: error storing credentials - err: exit status 1, out: `error getting credentials - err: exit status 1, out: `no usernames for https://index.docker.io/v1/``
govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ 
```


That error is a bit of a classic "Docker loop." Essentially, your Docker CLI is trying to use a helper (like `docker-credential-secretservice` or `pass`) to store your login token, but it’s tripping over an existing, messy configuration file.

Specifically, it's failing to save the new credentials because it’s getting confused by how it’s currently trying to "get" or "store" them for the Docker Index.

### The Quick Fix

The most reliable way to solve this is to reset your `config.json` file.

1. **Backup and remove the current config:**
```bash
rm ~/.docker/config.json
```


*(Don't worry, Docker will recreate a fresh one when you try to login again.)*
2. **Try logging in again:**
```bash
docker login -u thange
```