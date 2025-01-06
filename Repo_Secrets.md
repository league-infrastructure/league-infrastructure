
# Managing Secrets with `git-crypt`

This repository uses [`git-crypt`](https://github.com/AGWA/git-crypt) to securely encrypt 
sensitive files, such as credentials, API keys, and configuration files, within 
the Git repository.

*Note* once you add the files, re-encryption will be automatic. So, there is a step for 
de-crypting them, but not for encrypting them. Just use git normally. 

## Retrevial of the secrets

The key for the encryption is stored the The League's LastPass account, in a
Secure Note with the name of the repository. It should be in the shared "Repository Secrets" folder. 

## Setup

### 1. Install `git-crypt`

To begin, install `git-crypt` on your local machine:

* Macos: `brew install git-crypt`
* Linux: `sudo apt install git-crypt`
* Windows: Follow the instructions on the [git-crypt repository](https://github.com/AGWA/git-crypt), or switch to a real operating system. 

### 2. Initialize `git-crypt`

Once `git-crypt` is installed, initialize it in the repository. This step sets up the necessary configuration files:

```bash
git-crypt init
```

### 3. Define Files to Encrypt

Specify the files you want to encrypt by adding them to `.gitattributes`. For example, to encrypt a file called `secrets.json`:

```bash
echo "secrets/* filter=git-crypt diff=git-crypt" >> .gitattributes
```

You can also encrypt other files by adding them to the same `.gitattributes` file.

### 4. Add and Commit the Encrypted Files

Now that you've defined the files to encrypt, commit them to the repository. They will be encrypted before being stored:

```bash
git add secrets/*
git commit -m "Add encrypted secrets"
```

### 5. Distribute Access Keys

To allow team members to unlock the repository and access the encrypted files, export the encryption key:

```bash
git-crypt export-key secrets.key
```

Share the key securely with your team (e.g., via a secure file transfer tool).

### 6. Unlock Repository for Team Members

When another team member clones the repository, they can unlock the encrypted files by importing the key you provided. 

Typically this means: 

  1. Share the LastPass password note with the developer.
  2. The developer downloads the `secrets.key` file and copies it into the repo root directory.
  3. The dev runs: `git-crypt unlock secrets.key`

Once unlocked, they can view, edit, and commit encrypted files as if they were unencrypted.

### 7. Use Git as Usual

You can now work with encrypted files in the repository as usual. `git-crypt` will automatically encrypt and decrypt the files as needed.

## Revoking Access

If you need to revoke access for a team member, rotate the encryption key and re-encrypt the repository:

```bash
git-crypt rekey
```

After running this command, distribute the new key to authorized users.

## Installing private repos in Dockerfiles. 

To install a private repo in a docker files ( which is most often the `leaguesync`
library ) you can use a Docker compose feature to mount a file with a Github token 
from the repo in the docker container, then run the clone operation. The secret
does not persist in the container. 

1. In the LastPass folder 'Repository Secrets` get the note 'jointheleague-it Github Clone Only Token`
  This note has a Github token that can only reat repos, on the jointheleague-it account.
2. Put the token in the file `secret/git_token.txt`
3. Add a `secrets` section to `docker-compose.yaml` file. See below.
4. Add a `RUN` statement to your docker file that references the secret. 

This does at the bottom of `docker-compose.yaml`

```yaml
secrets:
  github_token:
    file: ./secrets/github_token.txt
```

Here is how you run with the secret ( loaded from the file into an env var ) in your `Dockerfile`
```Dockerfile
RUN --mount=type=secret,id=github_token GITHUB_TOKEN=$(cat /run/secrets/github_token) && \
    git clone https://${GITHUB_TOKEN}@github.com/league-infrastructure/leaguesync.git /opt/app/leaguesync
```

## Troubleshooting

If you encounter issues with `git-crypt`, consult the [official documentation](https://github.com/AGWA/git-crypt) or seek help in the repository's issues section.
