---
title: "Включаємо PhotoPrism за стіною NGINX і в суб-директорії (папці)"
description: ""
date: 2022-05-09T21:34:53+02:00
publishdate: 2022-05-09 21:34
lastmod: 2022-05-09 21:34
tags:
  - general
  - server
  - web
  - webserver
  - configs
  - setup
  - linux
  - nginx
  - photoprism
draft: false
---

[PhotoPrism](https://docs.photoprism.app/) - чудова веб програма для організації, сортування, класифікації фото; [Nginx](https://nginx.org/) - усі знають цей веб сервер, веб проксі і ще багато чого веб.

До недавнього часу PhotoPrism складно налаштовувався в контексті роботи за стіною, тобто за надійним веб проксі сервером в контексті доступу із суб-директорії, наприклад, `https://example.com/photoprism`. З останніми оновленнями усе запрацювало. Внизу інфо й налаштування як цього досягнути. Налаштування зроблені завдяки:

 - пану [@scolby33](https://github.com/photoprism/photoprism/issues/939#issue-790683829), який підняв питання про запуск такої схеми;
 - [опису, зазначеному в документації PhotoPrism](https://docs.photoprism.app/getting-started/advanced/nginx-proxy-setup/)

Черговість налаштування не має значення.

#### Nginx

Налаштування основного домену `example.com` (_example.com.conf_) уже є, доналаштовуємо лише _суб директорію_ `/photoprism`.

```nginx
    #include photoprism's configurations
    location /photoprism/ {
        # - - - - - - - - -
        # Reverse Proxy
        # - - - - - - - - -
        client_max_body_size 500M;

        proxy_redirect    off;
        proxy_set_header  X-Real-IP $remote_addr;                        # Let PP know the clients real IP
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;

        proxy_pass http://127.0.0.1:8484;

        proxy_buffering off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header X-Forwarded-Proto $scheme;                             # Let PP know that this connection used HTTP or HTTPS
    }

```

**Важливо!**: в директиві `location /photoprism/` вказати слеш в кінці (**trailing slash**).

В `proxy_pass` змінено порт на **8484** через зміну цього порта в налаштуваннях _Photoprism_ (див.нижче)

#### Photoprism configs

```config
version: '3.5'

# Example Docker Compose config file for PhotoPrism (Linux / AMD64)
#
# Note:
# - Running PhotoPrism on a server with less than 4 GB of swap space or setting a memory/swap limit can cause unexpected
#   restarts ("crashes"), for example, when the indexer temporarily needs more memory to process large files.
# - If you install PhotoPrism on a public server outside your home network, please always run it behind a secure
#   HTTPS reverse proxy such as Traefik or Caddy. Your files and passwords will otherwise be transmitted
#   in clear text and can be intercepted by anyone, including your provider, hackers, and governments:
#   https://docs.photoprism.app/getting-started/proxies/traefik/
#
# Documentation : https://docs.photoprism.app/getting-started/docker-compose/
# Docker Hub URL: https://hub.docker.com/r/photoprism/photoprism/
#
# DOCKER COMPOSE COMMAND REFERENCE
# see https://docs.photoprism.app/getting-started/docker-compose/#command-line-interface
# --------------------------------------------------------------------------
# Start    | docker-compose up -d
# Stop     | docker-compose stop
# Update   | docker-compose pull
# Logs     | docker-compose logs --tail=25 -f
# Terminal | docker-compose exec photoprism bash
# Help     | docker-compose exec photoprism photoprism help
# Config   | docker-compose exec photoprism photoprism config
# Reset    | docker-compose exec photoprism photoprism reset
# Backup   | docker-compose exec photoprism photoprism backup -a -i
# Restore  | docker-compose exec photoprism photoprism restore -a -i
# Index    | docker-compose exec photoprism photoprism index
# Reindex  | docker-compose exec photoprism photoprism index -f
# Import   | docker-compose exec photoprism photoprism import
#
# To search originals for faces without a complete rescan:

# docker-compose exec photoprism photoprism faces index
#
# All commands may have to be prefixed with "sudo" when not running as root.
# This will point the home directory shortcut ~ to /root in volume mounts.

services:
  photoprism:
    ## Use photoprism/photoprism:preview for testing preview builds:
    image: photoprism/photoprism:latest
    depends_on:
      - mariadb
    ## Don't enable automatic restarts until PhotoPrism has been properly configured and tested!
    ## If the service gets stuck in a restart loop, this points to a memory, filesystem, network, or database issue:
    ## https://docs.photoprism.app/getting-started/troubleshooting/#fatal-server-errors
    # restart: unless-stopped
    security_opt:
      - seccomp:unconfined
      - apparmor:unconfined
    ports:
      - "8484:2342" # HTTP port (host:container)
    environment:
      PHOTOPRISM_ADMIN_PASSWORD: "insecure"          # !!! PLEASE CHANGE YOUR INITIAL "admin" PASSWORD !!!
      PHOTOPRISM_SITE_URL: "https://example.com/photoprism/"  # public server URL incl http:// or https:// and /path, :port is optional
      PHOTOPRISM_ORIGINALS_LIMIT: 5000               # file size limit for originals in MB (increase for high-res video)
      PHOTOPRISM_HTTP_COMPRESSION: "gzip"            # improves transfer speed and bandwidth utilization (none or gzip)
      PHOTOPRISM_DEBUG: "false"                      # run in debug mode (shows additional log messages)
      PHOTOPRISM_PUBLIC: "false"                     # no authentication required (disables password protection)
      PHOTOPRISM_READONLY: "false"                   # don't modify originals directory (reduced functionality)
      PHOTOPRISM_EXPERIMENTAL: "false"               # enables experimental features
      PHOTOPRISM_DISABLE_CHOWN: "false"              # disables storage permission updates on startup
      PHOTOPRISM_DISABLE_WEBDAV: "false"             # disables built-in WebDAV server
      PHOTOPRISM_DISABLE_SETTINGS: "false"           # disables Settings in Web UI
      PHOTOPRISM_DISABLE_TENSORFLOW: "false"         # disables all features depending on TensorFlow
      PHOTOPRISM_DISABLE_FACES: "false"              # disables facial recognition
      PHOTOPRISM_DISABLE_CLASSIFICATION: "false"     # disables image classification
      PHOTOPRISM_DARKTABLE_PRESETS: "false"          # enables Darktable presets and disables concurrent RAW conversion
      PHOTOPRISM_DETECT_NSFW: "false"                # flag photos as private that MAY be offensive (requires TensorFlow)
      PHOTOPRISM_UPLOAD_NSFW: "true"                 # allows uploads that MAY be offensive
      # PHOTOPRISM_DATABASE_DRIVER: "sqlite"         # SQLite is an embedded database that doesn't require a server
      PHOTOPRISM_DATABASE_DRIVER: "mysql"            # use MariaDB 10.5+ or MySQL 8+ instead of SQLite for improved performance
      PHOTOPRISM_DATABASE_SERVER: "mariadb:3306"     # MariaDB or MySQL database server (hostname:port)
      PHOTOPRISM_DATABASE_NAME: "photoprism"         # MariaDB or MySQL database schema name
      PHOTOPRISM_DATABASE_USER: "photoprism"         # MariaDB or MySQL database user name
      PHOTOPRISM_DATABASE_PASSWORD: "insecure"       # MariaDB or MySQL database user password
      PHOTOPRISM_SITE_TITLE: "PhotoPrism"
      PHOTOPRISM_SITE_CAPTION: "AI-Powered Photos App"
      PHOTOPRISM_SITE_DESCRIPTION: ""
      PHOTOPRISM_SITE_AUTHOR: ""
      ## Run/install on first startup (options: update, gpu, tensorflow, davfs, nano, clean):
      PHOTOPRISM_INIT: "gpu tensorflow"
      ## Hardware video transcoding config (optional)
      # PHOTOPRISM_FFMPEG_BUFFERS: "64"              # FFmpeg capture buffers (default: 32)
      # PHOTOPRISM_FFMPEG_BITRATE: "32"              # FFmpeg encoding bitrate limit in Mbit/s (default: 50)
      # PHOTOPRISM_FFMPEG_ENCODER: "h264_v4l2m2m"    # use Video4Linux for AVC transcoding (default: libx264)
      # PHOTOPRISM_FFMPEG_ENCODER: "h264_qsv"        # use Intel Quick Sync Video for AVC transcoding (default: libx264)
      ## Run as a specific user, group, or with a custom umask (does not work together with "user:")
      # PHOTOPRISM_UID: 1000
      # PHOTOPRISM_GID: 1000
      # PHOTOPRISM_UMASK: 0000
      HOME: "/photoprism"
    ## Start as a non-root user (see https://docs.docker.com/engine/reference/run/#user)
    # user: "1000:1000"
    ## Share hardware devices with FFmpeg and TensorFlow (optional):
    # devices:
    #  - "/dev/dri:/dev/dri"
    #  - "/dev/nvidia0:/dev/nvidia0"
    #  - "/dev/nvidiactl:/dev/nvidiactl"
    #  - "/dev/video11:/dev/video11" # Video4Linux (h264_v4l2m2m)
    working_dir: "/photoprism"
    ## Storage Folders: "~" is a shortcut for your home directory, "." for the current directory
    volumes:
      # "/host/folder:/photoprism/folder"                # example
      - "~/Pictures:/photoprism/originals"               # original media files (photos and videos)
      # - "/example/family:/photoprism/originals/family" # *additional* media folders can be mounted like this
      - "~/Import:/photoprism/import"                  # *optional* base folder from which files can be imported to originals
      - "./storage:/photoprism/storage"                  # *writable* storage folder for cache, database, and sidecar files (never remove)

  ## Database Server (recommended)
  ## see https://docs.photoprism.app/getting-started/faq/#should-i-use-sqlite-mariadb-or-mysql
  mariadb:
    ## If MariaDB gets stuck in a restart loop, this points to a memory or filesystem issue:
    ## https://docs.photoprism.app/getting-started/troubleshooting/#fatal-server-errors
    restart: unless-stopped
    image: mariadb:10.6
    security_opt:
      - seccomp:unconfined
      - apparmor:unconfined
    command: mysqld --innodb-buffer-pool-size=128M --transaction-isolation=READ-COMMITTED --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --max-connections=512 --innodb-rollback-on-timeout=OFF --innodb-lock-wait-timeout=120
    ## Never store database files on an unreliable device such as a USB flash drive, an SD card, or a shared network folder:
    volumes:
      - "./database:/var/lib/mysql" # important, don't remove
    environment:
      MYSQL_ROOT_PASSWORD: insecure
      MYSQL_DATABASE: photoprism
      MYSQL_USER: photoprism
      MYSQL_PASSWORD: insecure

  ## Watchtower upgrades services automatically (optional)
  ## see https://docs.photoprism.app/getting-started/updates/#watchtower
  #
  # watchtower:
  #   restart: unless-stopped
  #   image: containrrr/watchtower
  #   environment:
  #     WATCHTOWER_CLEANUP: "true"
  #     WATCHTOWER_POLL_INTERVAL: 7200 # checks for updates every two hours
  #   volumes:
  #     - "/var/run/docker.sock:/var/run/docker.sock"
  #     - "~/.docker/config.json:/config.json" # optional, for authentication if you have a Docker Hub account
```

**Важливо!**, це

 > порт хосту змінено на 8484 в порівняні із налаштуваннями по замовчуванню. Порт можна не змінювати, це за бажанням. Зміну здійснено аби відповідати конфігурації Nginx ->

```
    ports:
      - "**8484**:2342" # HTTP port (host:container)
```


 > змінити основний пароль ->

```
      PHOTOPRISM_ADMIN_PASSWORD: "insecure"          # !!! PLEASE CHANGE YOUR INITIAL "admin" PASSWORD !!!
```


 > повністю прописати адресу (шлях) до суб директорії, включаючи домен та слеш в кінці шляху ->

```
      PHOTOPRISM_SITE_URL: "https://example.com/photoprism/"  # public server URL incl http:// or https:// and /path, :port is optional
```


 > додатково включено це налаштування, аби гарно впізнавались обличчя ->

```
      PHOTOPRISM_INIT: "gpu tensorflow"
```