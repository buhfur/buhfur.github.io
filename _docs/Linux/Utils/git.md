---
title: Git Snippets
parent: Utils
grand_parent: Linux
nav_order: 70
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

--- 

# Git CLI snippets  

- Move recent changes into a separate local branch 
    ```bash
    git stash 
    git checkout -b <branch-name>
    git stash pop 
    ```

> Note: You don't need to include `-b` if the local branch you are checking out to already exists. 

- Remove local branch 
    ```bash
    git branch -d <branch-name> 
    ```

- Merge two local branches together 
    1. Switch to target branch you want to merge changes into 
        ```bash
        git checkout <branch-name>
        ```
    2. Merge branch and resolve conflicts 
        ```bash
        git merge <branch-with-changes>
        ```

- Get upstream URL for local branch 
    ```bash
    git rev-parse --abbrev-ref --symbolic-full-name @{u}
    ```
- Get remote branches from git repo 
    ```bash
    git ls-remote --heads <repo-url>
    ```
- Clone specifc remote branch to local 
    ```bash
    git clone -b branch-name <repo-url>
    ```
- Delete remote branch on local machine 
    ```bash
    git push origin --delete branch-name 
    ```
- Create new local branch and add to remote 
    ```bash
    git checkout -b <branch-name> 
    git push -u origin <branch-name> 
    ```
- Change URL used for "origin" remote 
    ```bash
    git remote set-url origin <link-here> 
    ```

- Ignore vim swap files
    ```bash
    *~
    ```

> Note: Add this to your .gitignore and commit changes 

- Cache ( store ) git credentials 
    ```bash
    git config credential.helper store
    ```

- Reset all changes made on branch to HEAD
    ```bash
    git reset --hard HEAD
    ```


- Store git credentials permanantly
    ```bash
    git config --global credential.helper store
    ```


- Undo recent changes ( commits )
    ```bash
    git reset --hard HEAD
    ```

> Note : This throws away all your recent uncommited changes


- Create new local branch 
    ```bash
    git checkout -b <branch name> 
    ```
- Push local branch to remote repo as separate branch 
```bash
git push <remote-name> <local-branch-name>:<remote-branch-name>
```

> WARNING: DO NOT omit the `:<remote-branch-name>` or the remote branch will be DELTED


- Get branch name
```bash
git rev-parse --abbrev-ref HEAD
```

- Push all local branches to remote
    ```bash
    git push --all -u
    ```

> Note : This is handy if you aren't sharing a repo with someone, I might use this more often than not since i'm a hobbyist with no partners on my projects. ( As of yet ) 


- Change default editor for commits
    ```bash
    git config --global core.editor vim
    ```

> Note : This sets the default editor to vim 

---

# Notes & Additional information 

## Where are my creds stored when using `git config credential.helper store` ?

The credentials are stored on disk in a text file in the home directory of the user , this location is consistent when using both `--global` and `--system` arguments  , this file by default only gives read / write permissions to the user who initiated the command. The file is stored under `~/.git-credentials`. Do note however that this file is in plain text so anyone who can compromise your account ( or malicious NPM modules ) will be able to view your password and token. 



--- 

# Git snippet

# Git Command Reference

## Repository Initialization

### Initialize a New Repository

```bash
git init
```

### Clone a Repository

```bash
git clone user@server:/srv/repo
```

### Clone a Specific Branch

```bash
git clone --single-branch -b customer-map user@server:/srv/repo
```

### Clone Into a Specific Directory

```bash
git clone user@server:/srv/repo my-project
```

---

# Repository Information

### Show Repository Status

```bash
git status
```

### Show Commit History

```bash
git log
```

### Compact Commit History

```bash
git log --oneline
```

### Graph View

```bash
git log --oneline --graph --all --decorate
```

### Show Current Branch

```bash
git branch --show-current
```

### Show Remotes

```bash
git remote -v
```

---

# Staging Changes

### Stage a File

```bash
git add file.txt
```

### Stage All Changes

```bash
git add .
```

### Unstage a File

```bash
git restore --staged file.txt
```

### Stage Deleted Files

```bash
git add -u
```

---

# Commits

### Commit Changes

```bash
git commit -m "Added feature"
```

### Commit All Tracked Changes

```bash
git commit -am "Updated files"
```

### Amend Last Commit

```bash
git commit --amend
```

### Amend Without Changing Message

```bash
git commit --amend --no-edit
```

---

# Branches

### List Local Branches

```bash
git branch
```

### List Remote Branches

```bash
git branch -r
```

### List All Branches

```bash
git branch -a
```

### Create Branch

```bash
git branch feature-x
```

### Create and Switch Branch

```bash
git checkout -b feature-x
```

or

```bash
git switch -c feature-x
```

### Switch Branch

```bash
git checkout main
```

or

```bash
git switch main
```

### Rename Current Branch

```bash
git branch -m new-name
```

### Delete Branch

```bash
git branch -d feature-x
```

### Force Delete Branch

```bash
git branch -D feature-x
```

---

# Remote Branches

### Push New Branch

```bash
git push -u origin feature-x
```

### Push Existing Branch

```bash
git push
```

### Fetch Remote Branches

```bash
git fetch
```

### Create Local Branch from Remote

```bash
git checkout -b feature-x origin/feature-x
```

### Track Existing Remote Branch

```bash
git branch --set-upstream-to=origin/feature-x
```

### Delete Remote Branch

```bash
git push origin --delete feature-x
```

---

# Remotes

### Add Remote

```bash
git remote add origin user@server:/srv/repo
```

### Change Remote URL

```bash
git remote set-url origin user@server:/srv/repo
```

### Remove Remote

```bash
git remote remove origin
```

### Rename Remote

```bash
git remote rename origin production
```

### Show Remote Details

```bash
git remote show origin
```

---

# Pulling and Fetching

### Fetch Updates

```bash
git fetch
```

### Pull Changes

```bash
git pull
```

### Pull Specific Branch

```bash
git pull origin main
```

### Pull Unrelated Histories

```bash
git pull origin main --allow-unrelated-histories
```

---

# Merging

### Merge Branch into Current Branch

```bash
git merge feature-x
```

### Merge Without Fast Forward

```bash
git merge --no-ff feature-x
```

### Abort Merge

```bash
git merge --abort
```

---

# Rebasing

### Rebase Current Branch onto Main

```bash
git rebase main
```

### Rebase Onto Remote Main

```bash
git fetch
git rebase origin/main
```

### Abort Rebase

```bash
git rebase --abort
```

### Continue Rebase

```bash
git rebase --continue
```

---

# Comparing Changes

### Compare Working Tree to HEAD

```bash
git diff
```

### Compare Staged Changes

```bash
git diff --cached
```

### Compare Branches

```bash
git diff main..feature-x
```

### Show Changed Files Between Branches

```bash
git diff --name-status main..feature-x
```

### Compare Remote Branches

```bash
git diff origin/main..origin/feature-x
```

---

# Viewing Files

### List Files in Commit

```bash
git ls-tree -r HEAD --name-only
```

### Show File History

```bash
git log -- file.txt
```

### Show File Contents from Branch

```bash
git show main:file.txt
```

---

# Stashing

### Save Changes

```bash
git stash
```

### Save with Description

```bash
git stash push -m "Work in progress"
```

### List Stashes

```bash
git stash list
```

### Restore Most Recent Stash

```bash
git stash pop
```

### Apply Without Removing

```bash
git stash apply
```

### Delete Stash

```bash
git stash drop stash@{0}
```

---

# Undo Changes

### Restore File

```bash
git restore file.txt
```

### Restore Entire Working Tree

```bash
git restore .
```

### Reset File to HEAD

```bash
git checkout HEAD -- file.txt
```

### Soft Reset

```bash
git reset --soft HEAD~1
```

### Mixed Reset

```bash
git reset HEAD~1
```

### Hard Reset

```bash
git reset --hard HEAD~1
```

### Reset to Remote

```bash
git fetch
git reset --hard origin/main
```

---

# Tags

### Create Tag

```bash
git tag v1.0
```

### Annotated Tag

```bash
git tag -a v1.0 -m "Release 1.0"
```

### Push Tag

```bash
git push origin v1.0
```

### Push All Tags

```bash
git push --tags
```

### Delete Local Tag

```bash
git tag -d v1.0
```

### Delete Remote Tag

```bash
git push origin :refs/tags/v1.0
```

---

# Working with Bare Repositories

### Create Bare Repository

```bash
git init --bare /srv/repo
```

### Clone Bare Repository

```bash
git clone user@server:/srv/repo
```

### Show References

```bash
git show-ref
```

---

# Exporting Files

### Archive Branch

```bash
git archive --format=tar.gz HEAD -o project.tar.gz
```

### Export Branch to Directory

```bash
git archive main | tar -x -C /tmp/export
```

---

# Cleaning

### Show Files to be Removed

```bash
git clean -nd
```

### Remove Untracked Files

```bash
git clean -fd
```

### Remove Untracked Files and Directories

```bash
git clean -fdx
```

---

# Troubleshooting

### Detect Current Upstream

```bash
git rev-parse --abbrev-ref --symbolic-full-name @{u}
```

### Show Tracking Branches

```bash
git branch -vv
```

### Find Unmerged Branches

```bash
git branch --no-merged
```

### Find Merged Branches

```bash
git branch --merged
```

### Verify Repository

```bash
git fsck
```

### Show Configuration

```bash
git config --list
```

### Show Origin URL

```bash
git config --get remote.origin.url
```

### Show Global Username

```bash
git config --global user.name
```

### Show Global Email

```bash
git config --global user.email
```

---

# Common Workflows

## Create Feature Branch

```bash
git checkout main
git pull
git checkout -b feature-x
```

## Push Feature Branch

```bash
git add .
git commit -m "Added feature"
git push -u origin feature-x
```

## Merge Feature Branch into Main

```bash
git checkout main
git pull
git merge feature-x
git push
```

## Update Feature Branch from Main

```bash
git checkout feature-x
git fetch origin
git merge origin/main
```

## Replace Remote Branch with Another Branch

```bash
git checkout tfind-staging
git push origin tfind-staging:tfind --force
```

## Create Local Branch from Remote

```bash
git fetch origin
git checkout -b customer-map origin/customer-map
```

## Synchronize Local Branch with Remote

```bash
git fetch origin
git reset --hard origin/main
```
