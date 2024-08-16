---
title: "Autosave in NeoVim"
description: "Autosave is possible thanks to plugin"
DID: ""
date: 2024-06-14T20:10:43+02:00
publishdate: 2024-06-14 20:10
lastmod: 2024-06-14 20:10
tags:
  - general
draft: true
---

The video showing the plugin [here](https://www.youtube.com/watch?v=W5fjlU4tSpw)

The plugin is [pocco81/auto-save.nvim](https://github.com/pocco81/auto-save.nvim), but development abandonned, the forled and updated version is [okuuva/auto-save.nvim](https://github.com/okuuva/auto-save.nvim).

## Installation

In the above video installation is done via 'Lazy.vim'. I use **vim-plug** for installing plugins. According to instructions on the github repository the plugin has to be installed as following:

```bash
Plug 'okuuva/auto-save.nvim'
lua << EOF
  require("auto-save").setup {
    -- your config goes here
    -- or just leave it empty :)
  }
EOF
```

At the first start pluging didn't worked for me. Then the problem was found (yes, it's trivial, but still): the configuration of the plugin (the *lua* script) has to be put after line ```vim.call('plug#end')``` (thanks to author of issue [#50](https://github.com/okuuva/auto-save.nvim/issues/50))

{{< discuss >}}
