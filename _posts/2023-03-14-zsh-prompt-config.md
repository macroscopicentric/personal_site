---
layout: post
title:  "Zsh Prompt Editing"
date:   2023-03-14 17:53:00
categories: how_things_work
---

I finally switched my default shell on my laptop (Mac) from bash to zsh. I'd never bothered tinkering with a more sophisticated shell before this, so it was interesting to figure out what zsh could do. But I was frustrated because I had a nice prompt in bash, and it took me a while to recreate it.

My bash prompt was pretty simple, it included my current directory, then my git branch, then status indicators for different types of dirty state in a git repo (specifically, staged vs. unstaged changes). It also used to have a unicode crown emoji, but after working with bash on a lot of hosts with older versions of bash that didn't do well with unicode, I'd pulled the crown out years ago. (I was hopeful that I could add it back in, at least for my personal computer!)

(Sidenote: I looked into oh-my-zsh and then decided it was too complicated as a framework for my needs, so I'll be ignoring it here.)

Replicating my current directory with color-coding was very easy in zsh. It was actually even easier than in bash, since zsh accepts some strings for colors rather than codepoints. After reading [the zsh prompt expansion docs](https://zsh.sourceforge.io/Doc/Release/Prompt-Expansion.html#Prompt-Expansion) and [this 2019 blog post about customizing your zsh prompt](https://scriptingosx.com/2019/07/moving-to-zsh-06-customizing-the-zsh-prompt/), I ended up with:

```zsh
PROMPT="%B%F{magenta}%1/%f%b 👑 "

```

Here I initiate bolded and magenta-colored text ("%B%F{magenta}"), then print one directory out of my working directory, using an absolute path regardless of whether I'm inside my home directory ("%1/"), then undo the magenta color and bolding ("%f%b").

Next I wanted to replicate the git-related part of my bash prompt. Here zsh is, again, more sophisticated than bash, and has a bunch of basic out-of-the-box git functionality. If you just want to display your branch name and/or dirty state, zsh can do it right out of the box. I found [git's own documentation of how to display git info in the zsh prompt](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-Zsh) super helpful, and it also linked directly to [another section of the zsh docs on zsh and version control systems](https://zsh.sourceforge.io/Doc/Release/User-Contributions.html#Version-Control-Information). After some trial and error (and more help from other people's blog posts), I had this:

```zsh
# grab current branch and display in prompt
# source: https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-Zsh
autoload -Uz vcs_info add-zsh-hook
precmd_vcs_info() { vcs_info }
precmd_functions+=( precmd_vcs_info )
setopt prompt_subst

# These lines are specifically for staged + unstaged changes
# source: https://salferrarello.com/zsh-git-status-prompt/
add-zsh-hook precmd vcs_info
zstyle ':vcs_info:*' check-for-changes true
zstyle ':vcs_info:*' unstagedstr ' *'
zstyle ':vcs_info:*' stagedstr ' +'

zstyle ':vcs_info:git:*' formats '(%b%u%c)'

PROMPT='%B%F{magenta}%1/%f%b ${vcs_info_msg_0_} 👑 '
```

This did everything described above (show the git branch and all dirty states using different symbols), which was almost what I wanted. But I also really wanted part or all of the git part of my prompt to be color-coded red if there was _any_ type of dirty state. After some spelunking through the git and zsh documentation, I decided that `vcs_info` was neat but just didn't have enough power to allow me to conditionally format the magic `vcs_info_msg_0` variable. So I switched tactics, and went back to an old and reliable standby: [the `git-prompt.sh` script in the git repo itself](https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh).

I'd used this script before, to do the same things in bash, but I hadn't yet installed or used it with zsh. So I explicitly included that in my .zshrc:

```zsh
source ~/.git-prompt.sh
```

And then I did something I've honestly never done before, and I went and read that script's whole dang docstring to figure out if there was an easy way to do what I wanted. And there was! Here's the final draft of my prompt, which felt very simple in the end:

```zsh
source ~/.git-prompt.sh

# allow prompt substitution, show dirty state in color (incl. untracked files), define prompt
# source: the git-prompt.sh (above) docstring
setopt prompt_subst
GIT_PS1_SHOWDIRTYSTATE=1
GIT_PS1_SHOWCOLORHINTS=1
GIT_PS1_SHOWUNTRACKEDFILES=1
precmd () { __git_ps1 "%B%F{magenta}%1/%f%b " "👑 " "| %s " }
```

(Second sidenote: I did switch from assigning to `PROMPT` to using `precmd ()` here because the git-prompt.sh docstring recommended it as faster, but I'm not convinced it's faster for me. I haven't looked into whether this is user error, or zsh being different, or something else I haven't thought of.)