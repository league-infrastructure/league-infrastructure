
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

## Troubleshooting

If you encounter issues with `git-crypt`, consult the [official documentation](https://github.com/AGWA/git-crypt) or seek help in the repository's issues section.
