---
layout: post
title: "Using the console on Windows"
date: "2016-05-21"
categories: 
  - "coding"
  - "devops"
tags: 
  - "git"
  - "linux"
  - "tools"
  - "windows"
---

I tell you a secret: I'm a Windows user. I don't use OSX, I don't use Linux, I use Windows. And I tell you something more: I like it ;)

Usually I develop Java or JavaScript applications which perfectly run under Windows, Linux, OSX or what so ever. So developing under Windows is no problem at all. Runtime environments, IDEs, editors - Windows has it all. However, people keep wondering how I can do the most simple task:

> How do you connect to another server? Do you use Putty? - Argh, no, I just type `ssh some.server.com` and I'm done.

Or:

> Do you use your IDE to work with GIT? Or do you have Source Tree? - Argh, no, I just type `git add .` and `git commit -m "..."` and I'm done.

But I also know, that not everybody is doing it like this. People use the weirdest tools and techniques when working under Windows. A lot of people use that f\*\*\*ing small Windows CMD, Putty with its broken key-format or [CygWin](https://www.cygwin.com) to be a little bit more Linux-like. But I don't like all of these. The Windows CMD is unusable, [Putty](http://www.putty.org) is unnecessary and CygWin is a monster you don't need. Here is what I do.

# Don't use the Windows default CMD

The first thing I do, is to don't use Windows' default CMD. Why? It can't even mark and copy things! However, there's an easy and **open-source alternative**: [ConEmu](https://conemu.github.io). ConEmu has all the simple things you expect: colors, tabs, resizable, copy-and-paste and much more. You can [get it from GitHub](https://conemu.github.io/) and it even works without installation.

[![2016-05-21 21_51_21-Settings](images/2016-05-21-21_51_21-Settings.png)](http://tuhrig.de/wp-content/uploads/2016/05/2016-05-21-21_51_21-Settings.png)

# Use GIT as a toolbox for Windows

The other thing I do, is to use [GIT](https://git-scm.com) as a toolbox for Windows. When people are talking about using the console, they are actually talking about using tools. They talks about SCP, SSH or CURL like they come with their console - but they don't! All of those things are just individual programs installed on their machine. They are not related to the command line! So why don't install them on Windows?

If you use [GIT](https://git-scm.com), you already have everything you need in the `bin` folder (e.g. on `C:\Program Files (x86)\Git\bin\`):

`ssh`, `scp`, `curl`, `cat`, `grep`, `less`, `sh`, `bash`, `ls`, `mv`, `cp`, `diff`, `gzip`, and much more...

[![2016-05-21 22_09_29-Using the console on Windows – Thomas Uhrig](images/2016-05-21-22_09_29-Using-the-console-on-Windows-–-Thomas-Uhrig.png)](http://tuhrig.de/wp-content/uploads/2016/05/2016-05-21-22_09_29-Using-the-console-on-Windows-–-Thomas-Uhrig.png)

The only thing you need to do is to put GIT's `bin` folder to your Windows variable path. You will have everything at a fingertip in your console.

[![2016-05-21 22_26_43-Environment Variables](images/2016-05-21-22_26_43-Environment-Variables.png)](http://tuhrig.de/wp-content/uploads/2016/05/2016-05-21-22_26_43-Environment-Variables.png)

# Put my SSH-certificates to my user-folder

The last thing I usually do is to put my SSH-certificates to my user folder at `C:\Users\tug\.ssh`, so that SSH can find them.

**Best regards,** Thomas

# More

- [http://stackoverflow.com/questions/60950/is-there-a-better-windows-console-window](http://stackoverflow.com/questions/60950/is-there-a-better-windows-console-window)
