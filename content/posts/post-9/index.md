---
title: "Орфографія у Vim"
description: ""
date: 2022-05-09T15:29:15+02:00
publishdate: 2022-05-09 15:29
lastmod: 2022-05-09 15:29
tags:
  - general
  - vim
  - vimtricks
  - configs
draft: false
---

В текстовому редакторі **Vim** вбудована можливість здійснювати орфографічну перевірку.

Робимо так (у відкритому редакторі):

```vim
#включити перевірку орфографії
:set spell

#додаємо мови для перевірки (локально, при відкриті нових файлів це потрібно повторити)
:setlocal spell spelllang=en,ua,pl,de,it

#якщо словники перевірки відсутні, редактор завантажить їх сам, перед цим уточнивши куди зберігати
# на всі питання відповідаємо позитивно 
# насолоджуємось перевіркою орфографії
```

Цей випуск став доступним завдяки таким дописам [1](https://www.saintcarre.fr/saintcarre/2018/08/correcteur-orthographe-vim.html) [2](https://www.reddit.com/r/vim/comments/dt1782/vim_spellcheck_and_apostrophe_management/)  [3](https://superuser.com/questions/133208/how-to-make-vim-spellcheck-remember-a-new-word)  [4](http://vimdoc.sourceforge.net/htmldoc/spell.html) 
