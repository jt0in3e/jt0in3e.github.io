---
title: "Fonts fonts fonts ..and fonts. On VoidLinux while using DWM window manager and a desire to use that fancy icons, emoji, glyphs"
description: ""
DID: ""
date: 2024-08-14T17:15:30+02:00
publishdate: 2024-08-14 17:15
lastmod: 2024-08-14 17:15
tags:
  - general
  - voidlinux
  - linux
  - fonts
  - fontconfig
  - setup
  - system
draft: false
---

#### So what's the plan

To use and see symbols, icons, glyphs like  󰋊 or this  or this 📶 specific fonts have to be installed. What should be a trivial task from first view sometimes can be very tramedous task and time consuming job. 

Basic install of VoidLinux does not iclude much of fonts for fancy symbols display. While installing X server /Xorg/ it is recommended to install x fonts metapackage

However this is not enough for rendering and displaying emoji and other specific symbols.

To have max symbols, icons, emoji rendering and displaying coverage do

###### install fonts

```bash
sudo xbps-install -S xorg-fonts noto-fonts-emoji noto-fonts-ttf noto-fonts-ttf-extra noto-fonts-ttf-variable noto-fonts-cjk nerd-fonts dejavu-fonts-ttf
```

###### set-up fonts config file

```
sudo nvim /etc/fonts/local.conf # nvim editor (change to vim, or nano, or whatever)
```

###### add the following configs to 

**/etc/fonts/local.conf***

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd">
<fontconfig>
 <!-- Apply text rasterization, hinting, and anti-aliasing -->
  <match target="font">
    <edit name="antialias" mode="assign">
      <bool>true</bool>
    </edit>
    <edit name="hinting" mode="assign">
      <bool>true</bool>
    </edit>
    <edit name="rgba" mode="assign">
      <const>rgb</const>
    </edit>
    <edit name="hintstyle" mode="assign">
      <const>hintslight</const>
    </edit>
    <edit name="lcdfilter" mode="assign">
      <const>lcddefault</const>
    </edit>
  </match>
  <!-- Configure default fonts & fallback fonts -->
  <!-- Replace fonts with preferred fonts -->
  <!-- Noto Emoji allows for emojis to render in all apps including the terminal, remove if not needed  -->
    <alias>
    <family>serif</family>
    <prefer>
     <family>Noto Serif</family>
     <family>Noto Color Emoji</family>
     <family>Noto Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>sans-serif</family>
    <prefer>
     <family>Noto Sans</family>
     <family>Noto Color Emoji</family>
     <family>Noto Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>sans</family>
    <prefer>
     <family>Noto Sans</family>
     <family>Noto Color Emoji</family>
     <family>Noto Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>monospace</family>
    <prefer>
     <family>JetBrainsMono</family>
     <family>Noto Mono</family>
     <family>Noto Color Emoji</family>
     <family>Noto Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>mono</family>
    <prefer>
     <family>JetBrainsMono</family>
     <family>Noto Mono</family>
     <family>Noto Color Emoji</family>
     <family>Noto Emoji</family>
    </prefer>
  </alias>
</fontconfig>
```

###### Install Xorg's tool ```xrdb``` 

to setup some X fonts' specific configs in ```.Xresources``` file.

```
sudo xbps-install -S xorg-xrdb
```

add the following to ~/.Xresources

```
Xft.autohint: 0
Xft.lcdfilter:  lcddefault
Xft.hintstyle:  hintfull
Xft.hinting: 1
Xft.antialias: 1
Xft.rgba: rgb
```

and run command ```xrdb -merge ~/.Xresources```.

###### Create required symbolic links for text rendering effects to work:

```
sudo ln -s /usr/share/fontconfig/conf.avail/10-sub-pixel-rgb.conf /etc/fonts/conf.d/
sudo ln -s /usr/share/fontconfig/conf.avail/10-hinting-slight.conf /etc/fonts/conf.d/
sudo ln -s /usr/share/fontconfig/conf.avail/11-lcdfilter-default.conf /etc/fonts/conf.d/
```

###### Refresh the font cache files by running the following command.

```
sudo fc-cache -fv && fc-cache -fv
```

###### Add font definition to DWM's config file (```config.h``` or ```config.def.h```)

```
static const char *fonts[]          = { "JetBrainsMono Nerd Font Mono:size=10:antialias=true:autohint=true" };
static const char dmenufont[]       = "JetBrainsMono Nerd Font Mono:size=10";
```

Now rebuild and reinstall DWM

```
make && sudo make install
```

###### Update X start configs to get fonts for Xord

**add to *.xinitrc***

```
xrdb -merge ~/.Xresources

# prepend fonts' paths to know by X
xset +fp $HOME/.local/share/fonts
xset fp rehash
```

###### Reboot.

#### Problems

###### Disk size

The Font JetBrainsMono of Nerd Font group is located in package ```nerd-fonts```. The installation of this package will consume ~ 11G of disk. **Too much** for just having nice symbols and icons. So there should be options to:

- use different symbols, icons, emoji, which is included in another font's *package*

- install separatelly just required font like *JetBrainsMono* and not to install whole nerd-fonts group of fonts

- use/find any other fonts group alternative which encompasses glyphs for rendering required symbols and icons


#### Used materials

[Arch Linux Font Improvement Guide](https://github.com/dajeed/arch-linux-font-improvement-guide)

[Fonts page on VoidLinux Docs](https://docs.voidlinux.org/config/graphical-session/fonts.html)

[Fonts page on Arch Linux WiKi](https://wiki.archlinux.org/title/Fonts#Fallback_font_order)

[Tuning Fontconfig on Linux From Scratch Docs](https://www.linuxfromscratch.org/blfs/view/svn/x/tuning-fontconfig.html#items-which-can-override-fontconfig)

[stackexchange: Determine missing font for a specific unicode character](https://unix.stackexchange.com/questions/593578/determine-missing-font-for-a-specific-unicode-character)

[superuser: How to check which font the system uses?](https://superuser.com/questions/528698/how-to-check-which-font-the-system-uses)

[reddit: Does anyone have an example of a "dwm" config.h "nerd-font" that I can copy? :/](https://www.reddit.com/r/dwm/comments/nuyb7e/does_anyone_have_an_example_of_a_dwm_configh/)

{{< discuss >}}
