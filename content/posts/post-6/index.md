---
title: "Виправити деякі несправності в шаблоні свого ж блогу"
date: 2022-05-07T06:14:16+02:00
draft: true
---

Якось при написані блогу `hugo serve -D` видало таку помилку:

```bash
Start building sites …
hugo v0.94.2+extended linux/amd64 BuildDate=unknown
Error: Error building site: failed to render pages: render of "home" failed: "/home/user/jt0in3e.github.io/themes/ink-free/layouts/index.html:41:13": execute of template failed: template: index.html:41:13: executing "index.html" at <slicestr .RawContent 0 120>: error calling slicestr: slice bounds out of range
Built in 24 ms
```

Про схожі помилки пишуть [тут](https://github.com/gohugoio/hugo/issues/2466) і [тут](https://discourse.gohugo.io/t/limit-the-summary/4103/8), наприклад, звідки й взято таке рішення (додано зміни до темлпейту теми):

`vim themes/ink-free/layouts/index.html`

```bash
<span class="description">
	{{ if isset .Params "description" }}
		{{ .Description }}
	{{ else if gt (len .Summary) 120 }}
		{{ slicestr .Summary 0 120 | markdownify }}...
	{{ else }}
		{{ .Summary | markdownify }}
	{{ end }}
</span>
