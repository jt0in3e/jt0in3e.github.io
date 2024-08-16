---
title: "Swap permanentally CAPSLOCK with ESC, also in external keyboard"
description: "we're going to setup permanent switch of functionality of ESC and CAPS LOCK keys"
DID: ""
date: 2023-11-18T11:24:15+01:00
publishdate: 2023-11-18 11:24
lastmod: 2023-11-18 11:24
tags:
  - general
  - linux
  - configurations
  - parameters
  - X11
  - X
  - Xorg
  - keyboard
draft: true
---

### Swapping keys ESC and CAPSLOCK

It might be helpful to swap functionality of keys ESC and CAPSLOCK to easily work in word processors like VIM or whatever.

To swap we can use utility like ```setxkbmap``` in terminal to swap only for current session 

```bash
setxkbmap -option "caps:swapescape"
```

setxkbmap sets the keyboard layout for the current X session only, but can be made persistent in xinitrc or xprofile. This overrides system-wide configuration

{{< discuss >}}
