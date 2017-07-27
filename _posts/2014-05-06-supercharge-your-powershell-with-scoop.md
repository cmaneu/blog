---
layout: post
title: Supercharge your PowerShell with Scoop
slug: supercharge-your-powershell-with-scoop
date: 2014-05-06 10:26:01Z
---

I'm currently investigating few subjects that include spending some time in a PowerShell shell (like 'advanced' git usage, puppetization of my Windows Dev machine, ...). and I'm still missing few things from *nix shells.

But this may come to an end, thanks to **[Scoop](http://scoop.sh)** !

Scoop is a command-line install for Powershell. At first sight, it seems very close to [chocolatey](http://chocolatey.org), but the range of packages available are more command-line oriented. 

The setup is quite straightforward: Just run the following command in your PowerShell prompt:

```
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

I've been able to solve some of my biggest annoyances of Powershell quite simply:

- **SUDO-like commands**: with `scoop install sudo`, you can then type `sudo yourcommand` and your commant will run in an elevated prompt. Of course, if you still have UAC activated, you'll be prompted. But this workflow is much better than take back your mouse to launch an elevated prompt,
- **SSH into powershell**: I occasionaly SSH remote machines (and more and more these times). I usually launch a "Git Shell" from [GitHub for Windows](http://windows.github.com), but it was not straightfoward. Now, with `scoop install openssh`, you just have to ssh into your box :),
- **Create and view a file**: with `scoop install less touch`, it's now easy,
- **A bunch of Unix utilities** with [BusyBox](http://blog.lukesampson.com/busybox-for-windows): wget, who, uudecode/uuencode, diff, ...

I'm still exploring the repository of [scoop packages](https://github.com/lukesampson/scoop/tree/master/bucket) to see what tools I can use, but so far, it's really enhance my Powershell usage.
