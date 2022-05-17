---
title: "Запустити уже нарешті VPN, бо модуль `wireguard` 'not found in directory /lib/modules/'"
description: ""
DID: "wireguard-module"
date: 2022-05-17T14:24:59+02:00
publishdate: 2022-05-17 14:24
lastmod: 2022-05-17 14:24
tags:
  - general
  - configs
  - server
  - VPN
  - wireguard
  - setup
  - module
  - kernel
  - dkms
draft: false
---

Після оновленя системи (`sudo apt update && sudo apt upgrade -y`) оновилось й ядро Лінукс. Опісля перестав працювати `wireguard` VPN з такою помилкою

``` bash
modprobe: FATAL: Module wireguard not found in directory /lib/modules/...
```

Причина - в заголовках ядра, тобто через відсутність `linux-headers` для оновленого ядра. Робимо:

``` bash
sudo apt install linux-headers-$(uname -r)
```

і все тепер працює.

{{< discuss >}}
