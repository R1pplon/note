# 理解 Docker 网络类型

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

`SCOPE` 列指示了该网络是仅限于单个主机（`local`），还是可以跨越 Docker Swarm 中的多个主机（`swarm`）。
