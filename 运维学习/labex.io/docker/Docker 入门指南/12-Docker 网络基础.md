## 介绍
在本实验中，我们将探索 Docker 网络的基础知识。Docker 网络使得容器之间以及容器与外部世界之间能够进行通信。我们将涵盖各种网络类型、创建自定义网络、连接容器以及管理网络配置。通过这些动手实践，你将为理解 Docker 网络的概念和应用打下坚实的基础。
## 理解 Docker 网络类型
Docker 提供了几种内置的网络驱动程序。让我们先从检查系统上的默认网络开始。

在终端中运行以下命令，列出所有可用的 Docker 网络：

```bash
docker network ls
```

此命令会列出 Docker 在你系统上创建的所有网络。你应该会看到类似如下的输出：

```
NETWORK ID     NAME      DRIVER    SCOPE
79dce413aafd   bridge    bridge    local
91199fc6ad2e   host      host      local
1078d2c781b6   none      null      local
```

让我们来分析一下这些默认的网络类型：

1. `bridge`：这是默认的网络驱动程序。如果你在启动容器时没有指定网络，它会自动连接到桥接（bridge）网络。位于同一个桥接网络上的容器可以通过它们的 IP 地址相互通信。
    
2. `host`：该驱动程序移除了容器与 Docker 主机之间的网络隔离。容器直接共享主机的网络命名空间，这意味着它直接使用主机的 IP 地址和端口空间。这在某些需要优化性能的场景中非常有用。
    
3. `none`：该驱动程序会禁用容器的所有网络功能。使用此网络类型的容器将无法访问外部网络或其他容器。当你想要完全隔离一个容器时，它非常有用。
    

`SCOPE` 列指示了该网络是仅限于单个主机（`local`），还是可以跨越 Docker Swarm 中的多个主机（`swarm`）。
## 检查默认桥接网络
既然我们已经看过了网络列表，现在让我们近距离观察一下默认的桥接网络。这个网络是由 Docker 自动创建的，除非另有说明，否则容器都会使用它。

运行以下命令来检查桥接网络：

```bash
docker network inspect bridge
```

此命令提供了关于桥接网络的详细信息，包括其子网、网关和已连接的容器。你会看到类似如下的输出（为了简洁已截断）：

```json
[
  {
    "Name": "bridge",
    "Id": "79dce413aafdd7934fa3c1d0cc97decb823891ce406442b7d51be6126ef06a5e",
    "Created": "2024-08-22T09:58:39.747333789+08:00",
    "Scope": "local",
    "Driver": "bridge",
    "EnableIPv6": false,
    "IPAM": {
      "Driver": "default",
      "Options": null,
      "Config": [
        {
          "Subnet": "172.17.0.0/16",
          "Gateway": "172.17.0.1"
        }
      ]
    },
    "Internal": false,
    "Attachable": false,
    "Ingress": false,
    "ConfigFrom": {
      "Network": ""
    },
    "ConfigOnly": false,
    "Containers": {},
    "Options": {
      "com.docker.network.bridge.default_bridge": "true",
      "com.docker.network.bridge.enable_icc": "true",
      "com.docker.network.bridge.enable_ip_masquerade": "true",
      "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
      "com.docker.network.bridge.name": "docker0",
      "com.docker.network.driver.mtu": "1500"
    },
    "Labels": {}
  }
]
```

让我们分析一下输出中的关键信息：

- `Subnet`：此网络中容器使用的子网是 `172.17.0.0/16`。这意味着容器将被分配该范围内的 IP 地址。
- `Gateway`：此网络的网关是 `172.17.0.1`。这是容器用来与其自身网络之外的网络进行通信的 IP 地址。
- `Containers`：此字段目前为空，因为我们还没有启动任何容器。
- `Options`：这些是桥接网络的各种配置选项。例如，`enable_icc` 设置为 "true" 意味着在该网络上允许容器间通信（Inter-Container Communication）。

在排查网络问题或需要配置容器与特定 IP 范围通信时，理解这些信息至关重要。
## 创建自定义桥接网络
虽然默认的桥接网络适用于许多场景，但创建自定义网络可以提供更好的隔离性和控制力。当你想要将相关的容器分组在一起，或者需要控制哪些容器可以相互通信时，自定义网络特别有用。

让我们创建一个名为 `my-network` 的自定义桥接网络：

```bash
docker network create --driver bridge my-network
```

此命令创建了一个新的桥接网络。由于 `bridge` 是默认驱动程序，这里的 `--driver bridge` 选项实际上是可选的，但为了清晰起见，我们将其包含在内。

现在，让我们验证新网络是否已创建：

```bash
docker network ls
```

你应该能在网络列表中看到 `my-network`：

```
NETWORK ID     NAME         DRIVER    SCOPE
1191cb61c989   bridge       bridge    local
91199fc6ad2e   host         host      local
47ac4e684a72   my-network   bridge    local
1078d2c781b6   none         null      local
```

我们的新网络 `my-network` 出现在列表中，确认它已成功创建。该网络现在可供容器连接。
## 将容器连接到网络
现在我们有了自定义网络，让我们创建两个容器并将它们连接到该网络。在这个例子中，我们将使用 `nginx` 镜像，它提供了一个轻量级的 Web 服务器。

运行以下命令来创建两个容器：

```bash
docker run -d --name container1 --network my-network nginx
docker run -d --name container2 --network my-network nginx
```

让我们分析一下这些命令：

- `-d`：此标志以分离模式（detached mode）运行容器，即在后台运行。
- `--name`：这为我们的容器分配了一个名称，方便以后引用。
- `--network`：这指定了容器应该连接到哪个网络。
- `nginx`：这是我们用来创建容器的镜像名称。

这些命令创建了两个名为 `container1` 和 `container2` 的后台容器，它们都连接到了我们的 `my-network`。

让我们验证容器是否正在运行并已连接到我们的网络：

```bash
docker ps
```

此命令列出所有正在运行的容器。你应该在输出中看到这两个容器：

```
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
1234567890ab   nginx     "/docker-entrypoint.…"   10 seconds ago   Up 9 seconds    80/tcp    container2
abcdef123456   nginx     "/docker-entrypoint.…"   20 seconds ago   Up 19 seconds   80/tcp    container1
```

输出显示两个容器都在运行 Nginx 镜像，并在容器内部暴露了 80 端口。
## 测试容器间通信
Docker 网络的一个关键优势是，位于同一网络上的容器可以使用它们的容器名称作为主机名相互通信。这使得在不需要知道 IP 地址的情况下，就能轻松设置服务间的通信。

让我们通过使用 `container1` 向 `container2` 发送请求来测试这一点：

```bash
docker exec container1 curl -s container2
```

让我们分析一下这个命令：

- `docker exec`：这告诉 Docker 在正在运行的容器内部执行命令。
- `container1`：这是我们要执行命令的容器名称。
- `curl -s container2`：这是我们在容器内部执行的命令。它向 `container2` 发送一个 GET 请求，`-s` 标志让 curl 以静默模式运行。

此命令在 `container1` 内部执行 `curl` 命令，向 `container2` 发送请求。你应该在输出中看到默认的 Nginx 欢迎页面的 HTML 内容：

```html
<!doctype html>
<html>
  <head>
    <title>Welcome to nginx!</title>
    <style>
      html {
        color-scheme: light dark;
      }
      body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
      }
    </style>
  </head>
  <body>
    <h1>Welcome to nginx!</h1>
    <p>
      If you see this page, the nginx web server is successfully installed and
      working. Further configuration is required.
    </p>

    <p>
      For online documentation and support please refer to
      <a href="http://nginx.org/">nginx.org</a>.<br />
      Commercial support is available at
      <a href="http://nginx.com/">nginx.com</a>.
    </p>

    <p><em>Thank you for using nginx.</em></p>
  </body>
</html>
```

这个成功的响应证明了 `container1` 可以使用容器名称与 `container2` 通信。Docker 内置的 DNS 服务器会将容器名称解析为网络内的 IP 地址。
## 暴露容器端口
默认情况下，自定义网络中的容器可以相互通信，但无法从 Docker 主机外部访问。为了使容器可以从主机或外部网络访问，我们需要暴露它的端口。

让我们创建一个带有暴露端口的新容器：

```bash
docker run -d --name exposed-container -p 8080:80 --network my-network nginx
```

让我们分析一下这个命令：

- `-d`：以分离模式运行容器。
- `--name exposed-container`：将容器命名为 "exposed-container"。
- `-p 8080:80`：将容器内部的 80 端口映射到主机的 8080 端口。
- `--network my-network`：将容器连接到我们的自定义网络。
- `nginx`：使用 Nginx 镜像。

此命令创建了一个名为 `exposed-container` 的新容器，将容器的 80 端口映射到主机的 8080 端口，并将其连接到我们的 `my-network`。

现在，你可以通过在 Web 浏览器中访问 `http://localhost:8080` 或使用 curl 从主机访问此容器的 Nginx 服务器：

```bash
curl localhost:8080
```

你应该会看到与之前相同的 Nginx 欢迎页面 HTML。不过，这一次我们是直接从主机访问容器，而不是从另一个容器访问。
## 使用主机网络
在某些场景下，如果你希望容器共享主机的网络栈，可以使用主机（host）网络驱动程序。这会移除容器与主机之间的网络隔离，这对于某些特定应用很有用，但由于可能存在端口冲突，应谨慎使用。

让我们使用主机网络创建一个容器：

```bash
docker run -d --name host-networked --network host nginx
```

此命令使用主机网络创建了一个名为 `host-networked` 的新容器。请注意，在使用主机网络时不能使用 `-p` 参数，因为容器已经在使用主机的网络接口了。

为了验证容器正在使用主机网络，我们可以检查其网络设置：

```bash
docker inspect --format '{{.HostConfig.NetworkMode}}' host-networked
```

此命令检查容器并格式化输出，仅显示 NetworkMode。它应该输出 `host`，确认容器正在使用主机网络。

使用主机网络时，容器共享主机的 IP 地址，并可以直接访问主机的所有网络接口。这对于最大化性能很有用，但也意味着容器使用的任何端口都将直接在主机上打开，如果不小心，可能会导致冲突。
## 总结
在本实验中，我们探索了 Docker 网络的基础知识。我们涵盖了各种网络类型、创建自定义网络、连接容器、测试容器间通信、暴露容器端口以及使用主机网络。这些概念构成了 Docker 网络的基础，对于设计和管理容器化应用至关重要。

我们学习了如何：

- 列出并检查 Docker 网络
- 创建自定义桥接网络
- 将容器连接到网络
- 测试容器间的通信
- 将容器端口暴露给主机
- 为容器使用主机网络

理解这些网络概念将帮助你设计更健壮、更安全的容器化应用。随着你继续使用 Docker，你会遇到更高级的网络场景，但我们在这里涵盖的原则将为你深入研究容器编排和微服务架构打下坚实的基础。

请记住，Docker 网络是一个强大的工具，它允许你为应用创建隔离的环境，同时仍然允许容器之间以及与外部世界进行受控的通信。练习这些概念并探索更多高级主题，以熟练掌握 Docker 网络管理。