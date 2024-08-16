---
title: "Hugo Deploy Error because of some configs depressiation"
description: ""
DID: ""
date: 2024-08-16T11:22:30+02:00
publishdate: 2024-08-16 11:22
lastmod: 2024-08-16 11:22
tags:
  - general
  - hugo
  - deploy
  - website
  - blog
  - github
draft: true
---

While deploying blog post with Hugo engine to Github, the following error received:

```
Run hugo --minify
Start building sites … 
hugo v0.132.1-1bde700dfc0770bb11eb8445aff1ab5abdccb46e linux/amd64 BuildDate=2024-08-13T10:10:10Z VendorInfo=gohugoio

ERROR deprecated: .Site.DisqusShortname was deprecated in Hugo v0.120.0 and will be removed in Hugo 0.133.0. Use .Site.Config.Services.Disqus.Shortname instead.
Total in 78 ms
Error: error building site: logged 1 error(s)
Error: Process completed with exit code 1.
```

The ```grep -Rnie "site.disqus" .``` returns two files where this config is used:

```
./layouts/_default/single.html:68:                      {{- if .Site.DisqusShortname -}}
./layouts/partials/disqus.html:10:              var disqus_shortname = '{{ .Site.DisqusShortname }}';
```

Then change it from **.Site.DisqusShortname** to **.Site.Config.Services.Disqus.Shortname**. And now it deploys.


{{< discuss >}}
