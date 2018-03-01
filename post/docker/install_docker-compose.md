---
title: "Install docker-compose"
date: 2018-02-20T21:07:46+08:00
tags: ["docker-compose"]
draft: false
---

------------------



# Install docker-compose

## Linux

### Download and put into directory

```bash
curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

### add execute right

```bash
chmod +x /usr/local/bin/docker-compose
```

### Test install

```bash
docker-compose --version
```

