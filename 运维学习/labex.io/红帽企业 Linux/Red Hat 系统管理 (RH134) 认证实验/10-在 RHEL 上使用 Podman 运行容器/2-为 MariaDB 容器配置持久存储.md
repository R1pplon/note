# 为 MariaDB 容器配置持久存储

在这一步中，你将学习如何为容器配置持久性存储。默认情况下，在容器内创建的任何数据都存储在一个可写层中，该层与容器的生命周期相关联。如果你移除容器，所有这些数据都会丢失。对于像数据库这样的有状态应用来说，这并不理想。为了解决这个问题，我们使用 Podman 卷或绑定挂载（bind mounts）将数据存储在宿主文件系统上，独立于容器。

首先，我们需要移除在上一步中创建的容器，因为我们将使用新的存储配置重新启动它。

停止正在运行的 `mariadb_server` 容器：

```bash
podman stop mariadb_server
```

你将看到容器的名称作为输出，确认命令已收到。

```plaintext
mariadb_server
```

现在，移除已停止的容器：

```bash
podman rm mariadb_server
```

同样，容器的名称将被回显。

```plaintext
mariadb_server
```

接下来，在你的宿主机上的 `~/project` 目录中创建一个目录。此目录将保存 MariaDB 数据库文件。

```bash
mkdir ~/project/mariadb_data
```

在 rootless 模式下使用 Podman 时，我们需要为挂载的目录设置正确的权限。MariaDB 容器以特定用户（UID 999）运行，因此我们需要确保该目录是可访问的。我们将使用 `--userns=keep-id` 标志并设置适当的权限：

```bash
chmod 755 ~/project/mariadb_data
```

现在，再次运行 MariaDB 容器。此命令与上一步中的命令类似，但增加了 `-v` 标志和 `--userns=keep-id` 以正确处理用户命名空间映射。`-v` 标志将宿主机上的 `~/project/mariadb_data` 目录挂载到容器内的 `/var/lib/mysql` 目录中，MariaDB 在该目录中存储其数据。我们使用 `$(pwd)/mariadb_data` 为 `podman` 命令提供所需的绝对路径。

```bash
podman run -d \
  --name mariadb_server \
  --userns=keep-id \
  -e MARIADB_ROOT_PASSWORD=supersecret \
  -e MARIADB_DATABASE=webappdb \
  -e MARIADB_USER=webappuser \
  -e MARIADB_PASSWORD=userpass \
  -v $(pwd)/mariadb_data:/var/lib/mysql:Z \
  mariadb:10.6
```

卷挂载上的 `:Z` 后缀告诉 Podman 使用私有的、未共享的标签重新标记内容，这对于 SELinux 兼容性很重要。

容器启动后，你可以验证数据是否正在存储在你的宿主机上。列出 `~/project/mariadb_data` 目录的内容。

```bash
ls -l ~/project/mariadb_data
```

由于容器的数据库引擎已经初始化，你将看到在 `~/project/mariadb_data` 内部创建了几个文件和目录。这确认了你的数据现在是持久的。即使你移除容器，这些数据也将保留。

```plaintext
total 110632
-rw-rw---- 1 labex labex    16384 May 20 10:45 aria_log.00000001
-rw-rw---- 1 labex labex       52 May 20 10:45 aria_log_control
-rw-rw---- 1 labex labex      983 May 20 10:45 ib_buffer_pool
-rw-rw---- 1 labex labex 12582912 May 20 10:45 ibdata1
-rw-rw---- 1 labex labex 50331648 May 20 10:45 ib_logfile0
-rw-rw---- 1 labex labex 50331648 May 20 10:45 ib_logfile1
drwx------ 2 labex labex     4096 May 20 10:45 mysql
drwx------ 2 labex labex     4096 May 20 10:45 performance_schema
drwx------ 2 labex labex     4096 May 20 10:45 sys
drwx------ 2 labex labex     4096 May 20 10:45 webappdb
```

你已经成功地配置了你的 MariaDB 容器以使用持久性存储，确保你的数据库数据将在容器重启和移除后仍然存在。
