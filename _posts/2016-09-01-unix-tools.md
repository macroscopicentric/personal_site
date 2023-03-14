---
layout: post
title:  "An Introduction to Command-Line Unix Tools"
date:   2016-09-01 22:23:48
categories: how_things_work
---
I'm relatively new to Unix tools, and when I began, I was frustrated because I felt like the only way I knew how to find new tools was to ask people with more experience. The goal of this post is to provide some starting places if you'd like to learn more about what you can do on the command line, but it's not intended to be an exhaustive list.

1. `apropos` - I'm listing this one first because hopefully it will help you find others! Searches your man pages for the keyword(s) you pass into it. Ex: `apropos networking` will search for man pages that include the word "networking."
2. `curl` - get data over a network, via basically any protocol you can think of. It has a LOT of flags and can do oodles of stuff. I once made a joke about how I'd be back in six hours after hunting through the curl docs to find the flag I needed. The joke was on me; it really did take me that long to read the entire man page. Ex: `curl http://www.example.com -v` will give you the HTTP response (including headers) for a GET to www.example.com. Alternative (Linux only): `wget`.
3. `netstat` - show network connections. Ex: [`netstat -tunapl`](https://writing.natwelch.com/post/581) (example is Linux only).
4. `ps` - list currently-running processes. Ex: `ps aux` will list all processes for all users, including the percentages of CPU and memory being used. Alternative: `pstree` is a Linux-only tool that will show you a graph of all parent/children process relationships.
5. `top` - live view of current processes, including things like CPU percentage.
6. `strace` (Linux only) - trace system calls. Alternative for Mac OSX: [`dtrace`](https://8thlight.com/blog/colin-jones/2015/11/06/dtrace-even-better-than-strace-for-osx.html). I'm not going to explain this one too much because [Julia Evans](http://jvns.ca/) wrote [a great zine on it](http://jvns.ca/strace-zine-unfolded.pdf). If you're not sure what system calls are, Julia's zine explains that too!
7. `/proc` (Linux only) - a virtual directory that keeps track of information related to currently running processes. Once you have a process's PID, you can go to `/proc/<PID>` to find a variety of info. Ex: `/proc/<PID>/fd` is a directory listing all of the file descriptors (files being touched) for the given process, and `/proc/<PID>/environ` is a file listing a process's environment.

#### Further Reading

The [TLDR Pages](http://tldr-pages.github.io/) project has simplified explanations of the content of some man pages.

If you're looking for something more exhaustive, the [Linux man-pages project](https://www.kernel.org/doc/man-pages/) might help.

Netflix has a great post on ["Linux Performance Analysis in 60 Seconds"](http://techblog.netflix.com/2015/11/linux-performance-analysis-in-60s.html) with a list of Unix tools useful for troubleshooting performance issues.
