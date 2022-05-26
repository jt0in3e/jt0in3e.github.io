---
title: "`resolv.conf`: використання одночасно декількох nameserver та зменшення таймауту для fallback якщо DNS сервер недоступний"
description: "захопити й використати в своїх цілях resolv.conf"
DID: "resolv-multiple-timeout"
date: 2022-05-26T19:52:45+02:00
publishdate: 2022-05-26 19:52
lastmod: 2022-05-26 19:52
tags:
  - general
  - configs
  - server
  - setup
  - dns
  - nameserver
  - resolver
  - resolv.conf
  - linux
draft: false
---

Використовуємо декілька DNS серверів на лінукс-машині й зменшуємо час та кількість спроб на визначення доступності DNS сервера, тобто якщо сервер не відповідає, швидко переходимо до наступного. Для цього використаємо налаштування `options timeout:1 attempts:1 rotate`:

```bash
options timeout:1 attempts:1 rotate
nameserver 192.168.1.2
nameserver 9.9.9.9
nameserver 1.1.1.1
```

{{< discuss >}}
