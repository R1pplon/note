# 连接两个 Docker 容器

现在我们已经有了容器化的应用程序，接下来让我们学习如何连接容器。在这一步中，我们将学习如何连接两个 Docker 容器，使它们能够相互通信。这是在 Docker 中创建多服务应用程序的关键过程。

## 任务

连接一个 Apache Web 服务器容器和一个 MySQL 数据库容器。

## 要求

- 你需要两个 Docker 容器：一个运行 Apache，另一个运行 MySQL。
- 机器上必须已安装 Docker。

## 提示

- 记得打开一个新的终端来执行此步骤，以免与之前的容器产生冲突。

## 示例结果

从 Apache 容器访问 MySQL：

1. 启动一个名为 `my_apache` 的 Apache 容器，基于 `httpd` 镜像，将宿主机端口 `80` 映射到容器端口 `80`。

   ```bash
   labex:project/ $ docker ps | grep my_apache
   a91a93216e84 httpd "httpd-foreground" 52 seconds ago Up 47 seconds 0.0.0.0:80- my_apache > 80/tcp
   ```

2. 启动一个名为 `my_mysql` 的 MySQL 容器，使用 `mysql` 镜像，并将 `MYSQL_ROOT_PASSWORD` 环境变量设置为 `password`。

   ```bash
   labex:project/ $ docker ps | grep mysql
   0cb864cf97c6 mysql "docker-entrypoint.s…" 42 seconds ago Up 35 seconds 3306/tcp, 33060/tcp my_mysql
   ```

3. 将 Apache 容器连接到 MySQL 容器。
   注意：此步骤不会有直接的命令输出，但连接信息将显示在容器的环境变量中。你可以使用 `docker inspect my_apache` 命令检查容器。

   ```bash
   labex:project/ $ docker ps | grep my_app
   859c201b7267 my-app "python app.py" 53 seconds ago Up 52 seconds 80/tcp my_app
   ```

4. 使用 `docker exec` 命令从 `my_app` 容器访问 MySQL 命令行界面（如果你连接的方式不同，则需要调整命令）。

   ```bash
   Welcome to the MariaDB monitor.  Commands end with ; or \g.
   Your MySQL connection id is 8
   Server version: 8.3.0 MySQL Community Server - GPL

   Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   MySQL [(none)]>
   ```
