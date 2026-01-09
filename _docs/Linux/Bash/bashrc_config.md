---
title: Bashrc configuration 
parent: Bash
grand_parent: Linux
nav_order: 20
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

## Purpose
The `.bashrc` file is executed for interactive non-login shells. It is commonly used to set environment variables, aliases, shell options, and prompt customizations.

> Disclosure: Initially Generated with ChatGPT, updated manually over time.  

---

## Common Configurations

### 1. **Aliases**
```bash
alias ll='ls -lah'
alias gs='git status'
alias ..='cd ..'
```

### 2. **Environment Variables**
```bash
export EDITOR=nano
export PATH="$HOME/bin:$PATH"
```

### 3. **Shell Options**
```bash
shopt -s histappend    # Append to history instead of overwriting
shopt -s autocd        # Allow entering directories without `cd`
```

### 4. **Prompt Customization**
```bash
# Basic prompt
export PS1="\u@\h:\w\$ "

# Colored prompt
export PS1="\[\e[32m\]\u@\h\[\e[m\]:\[\e[34m\]\w\[\e[m\]\$ "
```

---

## History Settings
```bash
HISTSIZE=1000
HISTFILESIZE=2000
HISTCONTROL=ignoredups:erasedups
```

---

## Path Management
```bash
# Add custom directory to PATH
export PATH="$HOME/scripts:$PATH"
```

---

## Functions
```bash
mkcd () {
  mkdir -p "$1"
  cd "$1"
}
```

---

## Load Custom Scripts
```bash
# Source other config files
[ -f ~/.bash_aliases ] && . ~/.bash_aliases
[ -f ~/.bash_exports ] && . ~/.bash_exports
```

---

## Conditional Logic
```bash
if [[ $EUID -eq 0 ]]; then
  echo "You are root"
fi
```

---

## Best Practices
- Keep `.bashrc` modular by splitting into separate files like `.bash_aliases`, `.bash_exports`
- Always backup before editing: `cp ~/.bashrc ~/.bashrc.bak`
- Reload after editing: `source ~/.bashrc`

---

## Reloading `.bashrc`
```bash
source ~/.bashrc
```

---

# Useful snippets 

## Remove Highlight from Directories in ls

Add this line into your bashrc, it keeps the color of directories but removes the bright green highlight
```bash
export LS_COLORS="di=34:ln=36:so=32:pi=33:ex=31:bd=33;01:cd=33;01:su=37;41:sg=30;43:tw=30;42:ow=00" # Removes annoying highlighting from ls 
```
