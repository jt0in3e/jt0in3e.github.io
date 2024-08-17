---
title: "Swap permanentally CAPSLOCK with ESC, also in external keyboard"
description: "we're going to setup permanent switch of functionality of ESC and CAPS LOCK keys"
DID: ""
date: 2023-11-18T11:24:15+01:00
publishdate: 2023-11-18 11:24
lastmod: 2024-08-17 15:32
tags:
  - general
  - linux
  - configurations
  - parameters
  - X11
  - X
  - Xorg
  - keyboard
draft: false
---

### Swapping keys ESC and CAPSLOCK

It might be helpful to swap functionality of keys ESC and CAPSLOCK to easily work in word processors like VIM or whatever.

To swap we can use utility like ```setxkbmap``` in terminal to swap only for current session 

```bash
setxkbmap -option "caps:swapescape"
```

setxkbmap sets the keyboard layout for the current X session only, but can be made persistent in xinitrc or xprofile. This overrides system-wide configuration. For example, to achive this, write down the following config to *~/.xinitrc* file:

```
setxkbmap -option caps:escape
```

To also set up keyboard layout and set keyboard shortcut *Alt + Shift* to change layout instead write this:

```
setxkbmap -layout "us,fr,ua,ru" -option "grp:alt_shift_toggle,caps:swapescape"
```

{{< discuss >}}
