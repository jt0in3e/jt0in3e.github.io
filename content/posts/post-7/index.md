---
title: "Додаємо дату публікації та дату останньої зміни до своїх дописів у Hugo"
description: ""
date: 2022-05-08T12:41:39+02:00
publishdate: 2022-05-08
lastmod: 2022-05-08
tags:
  - general
  - hugo
  - website
  - blog
  - post
  - template
  - configs
  - webblog
  - html
draft: false
---

Цей блог пишеться на [Hugo](https://gohugo.io) з використанням теми [ink-free](https://github.com/chollinger93/ink-free). Додав відображення дати публікації допису й дати останньої зміни допису. Посилання на використанні матеріали внизу.

В налаштуваннях сайту у файлі `config.toml` визначаємо звідки і як формуватиметься дата останніх змін. Це означає, що є доступні такі можливості, як
- створити т.зв. ручний режим запису дати останньої зміни, це коли у файлах налаштувань теми та макету (layout) ми вставляємо код, що відслідковує зміни
- довіритись `Git` , що відслідковує зміни у файлах, при цьому, звісно, репозиторій гіт повинен розміщуватись в кореневій папці сайту

Наш вибір - гіт. 

У `config.toml` прописуємо::

```bash
enableGitInfo = true
[frontmatter]
  date = ["date", "publishDate", "lastmod"]
  lastmod = [":git", "lastmod", "date", "publishDate"]
  publishDate = ["publishDate", "date"]
  expiryDate = ["expiryDate"]
```
, де черговість визначення дати змін вказанно у перемінній `lastmod`, яку використовує Хюго для відображення дати змін. enableGitInfo - це те, що забезпечуватиме відслідковування дати зміни допису.

Далі у файлі `archetypes/post.md` вказуємо:

```bash
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
publishdate: {{ now.Format "2006-01-02 15:04" }}
lastmod: {{ now.Format "2006-01-02 15:04" }}
draft: true
---
```

Формат дати вказано з урахуванням часу аби в шаблоні файлів дописів додати дату та час останніх змін.

Усі макети, шаблони (templates, layouts) копіюємо локально із папки нашої теми, зокрема:

```bash
mkdir layouts/_default
cp themes/ink-free/layouts/_default/single.html layouts/_default/
cp themes/ink-free/layouts/index.html layouts/
```

Змінюємо, додаємо:

```bash
vim layouts/_default/single.html

# додаємо поле перед вмістом

#...
					<span><b>published:</b> <u>{{ .PublishDate.Format "January 2, 2006" }}</u>{{ if ne .PublishDate .Lastmod }}, <b><i>updated:</i></b> <i>{{ .Lastmod.Format "January 2, 2006; 15:04" }}</i>{{ end }}</span>
				</div>
				{{ .Content }}
#...
```

І звичайно робимо `git add .; git commit -am "super cool commit"`,і voilà, дата змін відображається в дописах, якщо зроблено зміни, очевидно )

_Використано:_
- [документація Хюго](https://gohugo.io/getting-started/configuration/#configure-dates)
- [ще із офіц.документації](https://gohugo.io/content-management/front-matter/)
- [формат дати й часу; офіц.документація](https://gohugo.io/functions/format/)
- [Blog post: Add a last edited date to posts](https://makewithhugo.com/add-a-last-edited-date/)
- [post of admin&dev of Hugo on the latter's forum: Configure how lastmod date is assigned](https://discourse.gohugo.io/t/configure-how-lastmod-date-is-assigned/14190) 
