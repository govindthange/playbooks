# How to create personal access tokens (PAT)?

Personal access tokens (PATs) provide a secure alternative to passwords for Docker CLI authentication. Use PATs to authenticate automated systems, CI/CD pipelines, and development tools without exposing your Docker Hub password.

> Treat access tokens like passwords and keep them secure. Store tokens in credential managers and never commit them to source code repositories.

To create a personal access token:

1. Sign in to [Docker Home](https://app.docker.com/).
2. Select your avatar in the top-right corner and from the drop-down menu select **Account settings**.
3. Select **Personal access tokens**.
4. Select **Generate new token**.
5. Configure your token:
   - **Description:** Use a descriptive name that indicates the token's purpose
   - **Expiration date:** Set an expiration date based on your security policies
   - **Access permissions:** **Read**, **Write**, or **Delete**.
6. Select **Generate**. Copy the token that appears on the screen and save it. You won't be able to retrieve the token once you exit the screen.

# How to authenticate using PAT?

### Step 1. Log in to Docker Desktop using web-based authentication flow.

### Step 2. Sign in to the Docker CLI using personal access token (PAT).

```console
$ docker login --username <YOUR_USERNAME>
Password: [paste your PAT here]
```

When prompted for a password, enter your personal access token instead of your Docker Hub password.

# How to fix `error storing credentials`?

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

### Step 1. Log in to Docker Desktop.

### Step 2. Backup and remove the current config.
```bash
rm ~/.docker/config.json
```

*(Don't worry, Docker will recreate a fresh one when you try to login again.)*

### Step 3. Logging in again via CLI.
```bash
docker login -u thange
```
For example:

```bash
govind@thinkpad:~/projects/ai-agents/agentic-email-crew/fingpt-poc$ docker login
Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

Username: thange
Password: 
WARNING! Your password will be stored unencrypted in /home/govind/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
govind@thinkpad:~/projects/ai-agents/agentic-wealth-advisor-crew/fingpt-poc$ 
```
