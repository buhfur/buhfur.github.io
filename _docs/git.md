---

layout: default
title: "Git Snippets"

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



