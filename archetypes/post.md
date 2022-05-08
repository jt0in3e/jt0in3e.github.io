---
title: "{{ replace .Name "-" " " | title }}"
description: ""
date: {{ .Date }}
publishdate: {{ now.Format "2006-01-02" }}
lastmod: {{ now.Format "2006-01-02" }}
tags:
  - general
draft: true
---

