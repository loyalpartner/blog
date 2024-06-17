+++
title = 'Build Task Sync Server'
date = 2024-06-17T15:37:42+08:00
draft = false
+++

# 部署 task 同步服务器

这里我选择用 docker 来部署 task 同步服务器，因为 docker 部署简单，而且可以保证环境的一致性。

准备 docker-compose.yml 文件：

```yaml
version: '3.8'

services:
  init-taskchampion:
    image: busybox
    volumes:
      - ~/.taskd:/var/lib/taskchampion-sync-server
    command: chown -R 100:100 /var/lib/taskchampion-sync-server
    deploy:
      restart_policy:
        condition: on-failure

  taskchampion:
    image: loyalparter/taskchampion-sync-server
    container_name: taskchampion
    ports:
      - "8080:8080"
    volumes:
      - ~/.taskd:/var/lib/taskchampion-sync-server
    depends_on:
      - init-taskchampion
    restart: unless-stopped
```

启动 task 同步服务器：

```bash
docker-compose up -d
```

