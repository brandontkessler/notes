# Bash Setup

Below should be added to .bashrc file or to .bash_profile file in root.

## Profile Setup

```bash
blue=$(tput setaf 33);
white=$(tput setaf 15);
yellow=$(tput setaf 11);
red=$(tput setaf 124);
green=$(tput setaf 64);
bold=$(tput bold);
reset=$(tput sgr0);

PS1="\[${bold}\]";
PS1+="\[${blue}\]\u";
PS1+="\[${white}\] at ";
PS1+="\[${yellow}\]\h";
PS1+="\[${white}\] in ";
PS1+="\[${green}\]\w";
PS1+="\n";
PS1+="\[${white}\]$ \[${reset}\]";

export PS1;
```

## Use PYTHONPATH for local packages

```bash
export PYTHONPATH='/Users/brandon.kessler/Python/pkgs'
```
