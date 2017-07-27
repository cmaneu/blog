---
layout: post
title: Use symbol fonts for your next Windows App
slug: use-symbol-fonts-for-your-next-windows-app
date: 0001-01-01 00:00:00Z
---

## Symbol fonts

Symbol fonts are used for some times. They gained attraction recently on the web and on Windows 8. They can be really useful for icons and action buttons. However, their usage need some rules to be used appropriately.

### Creating a symbol font

#### Do not use letters or numbers as symbol glyphs

Letters must be kept for letters usage, not symbols. Use hex code (like xE001). You can create System:String resources, like `<x:String x:Key="MyGlyph">&#xA010;</x:String>`.

#### On choosing a glyph code

> TBD Glyph code pages
> TBD Keep in sync bw font editor and code

#### Symbol font workflow

##### Illustrator/Photoshop tips

TBD

##### Tools to create/maintain Symbol font

TBD

- icomoon,
- fontastic,
- Desktop tools

### Performance impact

