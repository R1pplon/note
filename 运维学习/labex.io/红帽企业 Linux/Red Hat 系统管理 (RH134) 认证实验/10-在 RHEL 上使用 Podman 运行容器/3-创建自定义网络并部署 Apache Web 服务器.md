# 创建自定义网络并部署 Apache Web 服务器

在这一步中，你将为你的容器创建一个自定义网络并部署一个 Apache Web 服务器。虽然 Podman 提供了默认网络，但使用自定义网络是最佳实践。它们提供了更好的隔离，并且，最重要的是，能够在容器之间实现自动 DNS 解析。这允许容器使用它们的名称相互通信，这比使用可能更改的 IP 地址更可靠。

首先，让我们为我们的应用程序创建一个自定义桥接网络。我们将它命名为 `webapp-network`。

```bash
podman network create webapp-network
```

该命令将输出新创建的网络的名称。

```plaintext
webapp-network
```

你可以列出所有 Podman 网络，以确认你的网络已成功创建。

```bash
podman network ls
```

你应该在列表中看到 `webapp-network`，以及默认网络。

```plaintext
NETWORK ID     NAME               DRIVER    SCOPE
...
f1e2d3c4b5a6   webapp-network     bridge    local
...
```

接下来，我们需要在这个新网络上重新创建我们的 `mariadb_server` 容器。由于此环境中的网络后端配置，我们无法将现有容器连接到新网络。相反，我们将停止并使用新的网络配置重新创建容器。

停止正在运行的 `mariadb_server` 容器：

```bash
podman stop mariadb_server
```

移除已停止的容器：

```bash
podman rm mariadb_server
```

现在，使用新网络重新创建 MariaDB 容器。此命令与上一步中的命令类似，但增加了 `--network webapp-network` 标志：

```bash
podman run -d \
  --name mariadb_server \
  --network webapp-network \
  --userns=keep-id \
  -e MARIADB_ROOT_PASSWORD=supersecret \
  -e MARIADB_DATABASE=webappdb \
  -e MARIADB_USER=webappuser \
  -e MARIADB_PASSWORD=userpass \
  -v $(pwd)/mariadb_data:/var/lib/mysql:Z \
  mariadb:10.6
```

现在，让我们部署我们的 Web 服务器。我们将使用官方的 Apache `httpd` 镜像。首先，在宿主机上创建一个目录来存储你的网站文件。

```bash
mkdir ~/project/webapp_content
```

在这个新目录中创建一个简单的 `index.html` 文件。这将是我们的 Web 应用程序的主页。

```bash
echo "<h1>Welcome to My Web App</h1>" > ~/project/webapp_content/index.html
```

为 webapp 内容目录设置正确的权限，以确保 Apache 容器可以访问这些文件：

```bash
chmod 755 ~/project/webapp_content
```

现在，运行 Apache `httpd` 容器。我们将把它连接到我们的 `webapp-network` 并将 `webapp_content` 目录挂载为卷。这确保了 Web 服务器可以提供我们刚刚创建的 `index.html` 文件。

```bash
podman run -d \
  --name web_server \
  --network webapp-network \
  -v $(pwd)/webapp_content:/usr/local/apache2/htdocs/:Z \
  httpd:2.4
```

让我们分解一下这些选项：

- `--network webapp-network`：将新容器连接到我们的自定义网络。
- `-v $(pwd)/webapp_content:/usr/local/apache2/htdocs/:Z`：这会将我们的本地 `webapp_content` 目录挂载到容器内的 `/usr/local/apache2/htdocs/`，这是 Apache 提供文件的默认目录。`:Z` 后缀告诉 Podman 使用私有的、未共享的标签重新标记内容，以实现 SELinux 兼容性。

验证两个容器是否都在运行。

```bash
podman ps
```

你现在应该在正在运行的容器列表中看到 `mariadb_server` 和 `web_server`。

```plaintext
CONTAINER ID  IMAGE                           COMMAND           CREATED         STATUS         PORTS       NAMES
6a3f46c0ab3a  docker.io/library/mariadb:10.6  mariadbd          29 seconds ago  Up 29 seconds  3306/tcp    mariadb_server
da5d52ce9c41  docker.io/library/httpd:2.4     httpd-foreground  7 seconds ago   Up 7 seconds   80/tcp      web_server
```

这两个容器现在都在同一个自定义网络上，并且可以通过名称相互通信。
