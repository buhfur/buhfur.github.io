
# Git info 

- remote branches use the "main" name as default unless it was created a while ago when they changed the default 
name from "master" to "main".



**Store git credentials permanantly**

`git config --global credential.helper store`


**undo recent changes ( commits )**

`git reset --hard HEAD`


NOTE : This throws away all your recent uncommited changes


**Create new local branch and push to remote repo**

`git checkout -b <branch name> `

Branch is automatically created when you push the branch to the remote server. Most times I substiute `<remote-name>` with "origin"

`git push <remote-name> <local-branch-name>:<remote-branch-name>`


WARNING: DO NOT omit the `:<remote-branch-name>` or the remote branch will be DELTED


**Get branch name**

`git rev-parse --abbrev-ref HEAD`

**Push all local branches to remote**

This is handy if you aren't sharing a repo with someone, I might use this more often than not since i'm a hobbyist with no partners on my projects. ( As of yet ) 

`git push --all -u`

**Change default editor for commits**

This sets the default editor to vim 

`git config --global core.editor vim`
