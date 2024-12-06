

# Dotfiles 

I found a helpful article on hacker news which is referenced in the arch wiki for tips on how to manage your dotfiles.

The link is pasted below : 

[Arch Wiki](https://wiki.archlinux.org/title/Dotfiles)

[Hackernews Article](https://news.ycombinator.com/item?id=11071754)


# Notes for myself

To configure this setup on a new machine , run the following commands 

```

git clone --bare https://github.com/buhfur/dotfiles $HOME/.dotfiles
alias dotfiles='/usr/bin/git --git-dir="$HOME/.dotfiles/" --work-tree="$HOME"'
dotfiles checkout
dotfiles config --local status.showUntrackedFiles no

```

I've even created a script to automate this process , you can find it in the install\_scripts directory on my github. Or you can click the link below to download it. 


[Dotfile Script Link](../install_scripts/install_dotfiles.sh)


---

**In order to utilize this setup , you will need to add your dotfiles to be tracked under the bare repo**

```

config status 
config add .vimrc 
config commit -m "Add vimrc"
config add .config/redshift.con
config commit -m "Added redshift config "
config push 
```


Someone else also made an article on this users approach to handling dotfiles , you may find this link below 

[Dotfiles: Best way to store in a bare git repository](https://www.atlassian.com/git/tutorials/dotfiles)

