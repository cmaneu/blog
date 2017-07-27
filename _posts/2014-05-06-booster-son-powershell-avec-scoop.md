---
layout: post
title: Booster son Powershell avec Scoop
slug: booster-son-powershell-avec-scoop
date: 2014-05-06 10:40:10Z
---

Je bosse sur plusieurs sujets en ce moment qui me font passer pas mal de temps dans Powershell (comme travailler sur Git, ou configurer ma machine de dev avec Puppet). Bien que j'adore Powershell, il me manque/j'ai toujours quelques réflexes qui me viennent des shells *nix.


Mais c'est peut-être la fin de ces désagréments, avec **[Scoop](http://scoop.sh)** !

Scoop est un outil d'installation en ligne de commande. J'ai d'abord cru à un clone de [chocolatey](http://chocolatey.org), mais les packages disponibles sont plus orientés outils en ligne de commande.

L'installation est simple, il suffit d'exécuter la commande suivante dans un prompt PowerShell.

```
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

J'ai réussi à résoudre mes plus grand soucis de PowerShell avec les packages suivants::

- **SUDO**: avec `scoop install sudo`, il est désormais possible de faire `sudo yourcommand` et notre commande sera exécutée dans un prompt élevé. Bien évidemment, si vous avez l'UAC d'activé, vous serez prompté, mais c'est bien plus pratique que de prendre sa souris pour lancer un nouveau prompt élevé,
- **SSH dans Powershell**: J'SSH de plus en plus sur des machines distances. D'habitude j'utilise le "Git Shell" de [GitHub for Windows](http://windows.github.com), mais encore une fois, un nouveau prompt à ouvrir. Désormais, avec `scoop install openssh`, j'ai juste à faire ssh mamachine. Plus besoin de Putty :),
- **Créer et voir des fichiers**: avec `scoop install less touch`,
- **Pleins d'autres outils Unix** avec [BusyBox](http://blog.lukesampson.com/busybox-for-windows): vi, wget, who, uudecode/uuencode, diff, ...

Je suis toujours en train de parcourir le [repository de packages scoop](https://github.com/lukesampson/scoop/tree/master/bucket) pour voir quels autres outils peuvent m'être utiles. Mais le peu que j'ai découvert a déjà amélioré significativement mon utilisation de PowerShell.
