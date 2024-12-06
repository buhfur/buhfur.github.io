
# Tmux notes 

**Run tmux with diff config file**

`tmux -f .something.conf`


**Change default terminal**

Put this in your .tmux.conf file , the example below changes the default shell to zsh  

`set -g default-shell /bin/zsh`

**Paste using the middle mouse button**

Hold Shift while pressing the middle mouse button in a tmux terminal. This will result in the standard OS behavior, allowing you to paste text into the terminal.

**Resource tmux file after making changes**

For additional info about the keybinds , check the manual. Do "man tmux "

do `Prefix + :`

Then do 
```bash 
:source ~/.tmux.conf 
```
