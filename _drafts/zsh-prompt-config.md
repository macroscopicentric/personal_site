---
layout: post
title:  "Zsh Prompt Editing"
date:   2023-03-14 15:11:00
categories: how_things_work
---

I finally switched my default shell on my laptop (Mac) from bash to zsh. I'd never bothered tinkering with a more sophisticated shell before this, so it was interesting to figure out what zsh could do. But I was frustrated because I had a nice prompt in bash, and it took me a while to recreate it.

My bash prompt was pretty simple, it included my current directory, then my git branch, then status indicators for different types of dirty state in a git repo (specifically, staged vs. unstaged changes). It also used to have a unicode crown emoji, but after working with bash on a lot of hosts with older versions of bash that didn't do well with unicode, I'd pulled the crown out years ago. (I was hopeful that I could add it back in, at least for my personal computer!)