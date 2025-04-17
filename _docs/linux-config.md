

# Small snippets for linux configuration 


## Set Vim as manpager 

Add this line in your .bashrc 

```bash
export MANPAGER='vim +Man!'
```


## Default Ranger config 

Run this line below. This will copy the default config files for ranger into your ~/.config/ranger/""

```bash
ranger --copy-config=all
```


## bspwm 

Restart window manager 

```bash
bspc wm -r 
```

