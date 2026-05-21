# 使用环境变量运行 MariaDB 数据库容器

在这一步中，你将学习如何运行一个容器化应用，并使用环境变量在启动时对其进行配置。这是容器管理中的一项基本技能，它允许进行灵活且安全的部署。我们将使用官方的 MariaDB 镜像作为示例，因为它需要几个配置参数来初始化数据库。

首先，确保你在正确的工作目录中。本实验（Lab）的所有工作都将在 `~/project` 目录内完成。

```bash
cd ~/project
```

在运行容器之前，最好明确地从注册中心拉取镜像。这可以确保你在本地拥有正确的版本。为了确保一致性，我们将在此实验（Lab）中使用 `mariadb:10.6` 镜像。

```bash
podman pull mariadb:10.6
```

从 Docker 注册中心选择 `mariadb:10.6` 镜像。

你应该看到输出，表明镜像正在被下载和提取。

```plaintext
10.6: Pulling from library/mariadb
...
Status: Downloaded newer image for mariadb:10.6
docker.io/library/mariadb:10.6
```

现在，你可以运行 MariaDB 容器了。`podman run` 命令创建并启动一个新的容器。我们将使用几个标志：

- `-d`：在分离模式（后台）运行容器。
- `--name mariadb_server`：为我们的容器分配一个易于记忆的名称。
- `-e VARIABLE=value`：在容器内设置一个环境变量。MariaDB 镜像使用这些变量在首次启动时配置数据库。

运行以下命令来启动你的 MariaDB 容器。我们正在设置 root 密码，并创建一个名为 `webappdb` 的新数据库，以及一个专用的用户 `webappuser`。

```bash
podman run -d \
  --name mariadb_server \
  -e MARIADB_ROOT_PASSWORD=supersecret \
  -e MARIADB_DATABASE=webappdb \
  -e MARIADB_USER=webappuser \
  -e MARIADB_PASSWORD=userpass \
  mariadb:10.6
```

该命令将输出一个长的容器 ID，这确认了容器已被创建。

```plaintext
a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
```

要验证容器是否正在运行，请使用 `podman ps` 命令。

```bash
podman ps
```

你应该在正在运行的容器列表中看到 `mariadb_server`。

```plaintext
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS      NAMES
a1b2c3d4e5f6   mariadb:10.6   "docker-entrypoint.s…"   15 seconds ago   Up 14 seconds   3306/tcp   mariadb_server
```

最后，让我们检查容器的日志，以确保数据库使用我们提供的环境变量正确初始化。

```bash
podman logs mariadb_server
```

滚动浏览日志。你正在寻找一个表明服务器已准备好连接的行，这确认了启动成功。输出将很长，但接近结尾的一个关键的成功消息如下所示：

```plaintext
...
2024-05-20 10:30:00+00:00 [Note] [Entrypoint]: /usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/
...
2024-05-20 10:30:15+00:00 [Note] mariadbd: ready for connections.
Version: '10.6.x-MariaDB-1:10.6.x+maria~ubu2004'  socket: '/run/mysqld/mysqld.sock'  port: 3306  mariadb.org binary distribution
```

你已经成功地使用环境变量启动并配置了一个 MariaDB 容器。
