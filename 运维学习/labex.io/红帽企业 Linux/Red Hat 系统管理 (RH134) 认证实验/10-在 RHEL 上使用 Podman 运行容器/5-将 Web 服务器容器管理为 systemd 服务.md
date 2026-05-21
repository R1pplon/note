# 将 Web 服务器容器管理为 systemd 服务

在最后一步中，你将学习如何配置一个容器以自动启动，确保你的服务对崩溃或系统重启具有弹性。在标准的 Red Hat Enterprise Linux 系统上，`systemd` 是管理服务的主要工具。但是，本实验中的 Podman 环境不直接使用 `systemd` 来管理容器。

相反，我们将使用 Podman 内置的**重启策略**来实现相同的结果——自动服务重启。这是确保容器由 Podman 守护进程自动启动的标准、容器原生方式。我们将配置我们的 `web_server`，以便在它因任何原因停止时始终重新启动。

首先，我们必须移除现有的容器，因为重启策略只能在创建容器时应用。

停止 `web_server` 容器：

```bash
podman stop web_server
```

```plaintext
web_server
```

现在移除它：

```bash
podman rm web_server
```

```plaintext
web_server
```

接下来，使用与之前相同的配置重新创建 `web_server` 容器，但添加 `--restart always` 标志。此标志指示 Podman 守护进程监视容器，并在它退出时重新启动它。

```bash
podman run -d \
  --name web_server \
  --network webapp-network \
  -v $(pwd)/webapp_content:/usr/local/apache2/htdocs/:Z \
  -p 8080:80 \
  --restart always \
  httpd:2.4
```

容器将照常启动。要确认重启策略已激活，你可以检查容器的配置。

```bash
podman inspect web_server --format '{{.HostConfig.RestartPolicy.Name}}'
```

该命令应返回 `always`，确认策略已设置。

```plaintext
always
```

现在，让我们演示重启策略的工作方式，通过手动重启容器来模拟系统重启或容器故障后会发生的情况。

首先，让我们检查当前的重启策略配置：

```bash
podman inspect web_server --format '{{.HostConfig.RestartPolicy.Name}}'
```

这应该显示 `always`，确认我们的重启策略已配置。

```plaintext
always
```

现在让我们测试手动重启，以模拟故障后的恢复：

```bash
podman start web_server
```

```plaintext
web_server
```

检查容器是否正在运行：

```bash
podman ps
```

你应该看到两个容器都在运行，并且重启策略已到位：

```plaintext
CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS              PORTS                  NAMES
e7f6g5h4i3j2   httpd:2.4      "httpd-foreground"       About a minute ago   Up 5 seconds        0.0.0.0:8080->80/tcp   web_server
a1b2c3d4e5f6   mariadb:10.6   "docker-entrypoint.s…"   About an hour ago    Up About an hour    3306/tcp               mariadb_server
```

最后，确认该服务可访问：

```bash
curl http://localhost:8080
```

```plaintext
<h1>Welcome to My Web App</h1>
```

**理解重启策略：**

你配置的 `--restart always` 策略确保：

- 如果容器意外退出，它将自动重启
- 当 Podman 服务启动时（例如，系统重启后），容器将自动启动
- 这为生产部署提供了弹性

注意：在某些实验环境中，自动重启行为可能会因 Podman 配置以及 Podman 系统服务是否正在运行而异。关键的学习目标是理解如何为生产部署配置重启策略。

你已经成功地将你的容器配置为像服务一样被管理，确保它自动保持可用。这完成了容器化应用程序的基本生命周期管理。
