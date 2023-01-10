---
layout: post
title: "Using the console on Windows"
date: "2016-05-21"
categories:
  - "devops"
---

I'm a [Windows](https://www.microsoft.com/de-de/windows/) user. 
I don't use [OSX](https://en.wikipedia.org/wiki/MacOS), I don't use Linux, I use Windows. For real.
Usually I develop Java or JavaScript applications which perfectly run under Windows, Linux, OSX or whatsoever. 
So developing under Windows is no problem at all. 
Runtime environments, IDEs, editors - Windows has it all. 
However, people keep wondering how I can do the most simple task:

> How do you connect to another server? Do you use Putty? - Argh, no, I just type `ssh some.server.com`.

Or:

> Do you use your IDE to work with GIT? Or Source Tree? - No, I just type `git add .` and `git commit -m "..."`.

But I also know, that not everybody is doing it like this. 
People use the weirdest tools and techniques when working under Windows:
Using the default Windows CMD, Putty with its broken key-format or [CygWin](https://www.cygwin.com) to be a little more Linux-like. 
But I don't like all of these. 
The Windows CMD is unusable, [Putty](http://www.putty.org) is unnecessary and CygWin is a monster you don't need. 
Here is what I do.

## Don't use the Windows default CMD

The first thing I do, is to don't use the default Windows CMD. 
Why? 
It can't even mark and copy things! 
However, there's an easy and **open-source alternative**: [ConEmu](https://conemu.github.io). 
ConEmu has all the simple things you expect: colors, tabs, resizable, copy-and-paste and much more. 
You can [get it from GitHub](https://conemu.github.io/) and it even works without installation.

![](/images/2016/05/console.png)

## Use GIT as a toolbox for Windows

The other thing I do, is to use [GIT](https://git-scm.com) as a toolbox for Windows. 
When people are talking about using the console, they are actually talking about using tools. 
They talk about SCP, SSH or CURL like they come with their console - but they don't! 
All of those things are just individual programs installed on their machine. 
They are not related to the command line! 
So why don't install them on Windows?

If you use [GIT](https://git-scm.com), you already have everything you need in the `bin` folder (e.g. on `C:\Program Files (x86)\Git\bin\`):

- `ssh`
- `scp`
- `curl`
- `cat`
- `grep`
- `less`
- `sh`
- `bash`
- `ls`
- `mv`
- `cp`
- `diff`
- `gzip`
- ...

The only thing you need to do is to put GIT's `bin` folder to your Windows variable path. 
You will have everything at a fingertip in your console.

![](/images/2016/05/variables.png)

## Put my SSH-certificates to my user-folder

The last thing I usually do is to put my SSH-certificates to my user folder at `C:\Users\tug\.ssh`, so that SSH can find them.
Nothing you wouldn't do on a Mac, too.

## More

- [http://stackoverflow.com/questions/60950/is-there-a-better-windows-console-window](http://stackoverflow.com/questions/60950/is-there-a-better-windows-console-window)

**Best regards,** Thomas.
