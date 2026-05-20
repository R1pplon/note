## 介绍
Docker 网络是容器编排的基础，它使容器能够相互通信以及与外部网络通信。本挑战将引导你掌握 Docker 网络的核心概念，从创建和管理网络到连接和断开容器。

通过完成本挑战，你将获得 Docker 网络命令的实战经验，并了解如何建立容器间的通信。这些技能对于在 Docker 环境中开发和部署多容器应用至关重要。

让我们一起深入探索 Docker 网络的世界吧！
## 创建网络
在这一步中，你将创建你的第一个 Docker 网络。这是连接容器并实现它们之间通信的基础。

### 任务

1. 使用 `docker network create` 命令创建一个名为 `my-network` 的新桥接网络（bridge network）。
2. 通过列出所有 Docker 网络来验证网络是否已成功创建。

### 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 创建网络时使用默认的桥接驱动（bridge driver）。

### 示例

完成此步骤后，运行 `docker network ls` 时应该能看到 `my-network`：

```
NETWORK ID     NAME         DRIVER    SCOPE
abcdef123456   my-network   bridge    local
```
## 在网络中启动容器
现在我们已经有了一个网络，让我们启动一些容器并将它们连接到该网络。这一步将演示如何在特定网络中启动容器，这是 Docker 网络中的一个关键概念。

### 任务

1. 使用 `nginx` 镜像启动一个名为 `container1` 的容器，并将其连接到 `my-network`。
2. 使用 `httpd` 镜像启动另一个名为 `container2` 的容器，并将其连接到 `my-network`。
3. 验证两个容器都在运行并已连接到 `my-network`。

### 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 启动容器时使用 `--network` 标志将其连接到 `my-network`。
- 以分离模式（detached mode）运行这两个容器。

### 示例

完成此步骤后，使用 `docker ps` 时应该能看到两个正在运行的容器：

```
CONTAINER ID   IMAGE   COMMAND   CREATED         STATUS         PORTS     NAMES
abcdef123456   nginx   "..."     2 minutes ago   Up 2 minutes   80/tcp    container1
fedcba654321   httpd   "..."     2 minutes ago   Up 2 minutes   80/tcp    container2
```
## 测试连通性
在这一步中，你将验证容器是否可以通过你创建的网络相互通信。这是一个关键测试，用于确保你的网络配置按预期工作。

### 任务

1. 使用 `docker exec` 命令在 `container1` 中运行一个 shell。
2. 在 `container1` 内部，使用 `curl` 命令通过容器名称访问 `container2`。

### 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 在 `curl` 命令中使用 `container2` 作为主机名。
- `curl` 命令应访问 `container2` 的默认 HTTP 端口（80）。

### 示例

在 `container1` 中执行 `curl` 命令后，你应该能看到 Apache HTTP Server 的默认页面内容：

```
<!DOCTYPE html>
<html><body><h1>It works!</h1></body></html>
```
## 将容器连接到多个网络
在这一步中，你将学习如何将单个容器连接到多个网络。这是一个更高级的概念，对于创建复杂的网络拓扑或在网络之间逐步迁移服务非常有用。

### 任务

1. 创建一个名为 `my-network2` 的新桥接网络。
2. 将 `container2` 连接到 `my-network2`，同时保持其与 `my-network` 的连接。
3. 验证 `container2` 现在是否已连接到这两个网络。

### 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 使用 `docker network connect` 命令将 `container2` 添加到 `my-network2`。
- 使用 `docker inspect` 命令验证网络连接情况。

### 示例

将 `container2` 连接到 `my-network2` 后，检查容器时应该能看到列出的两个网络：

```
"Networks": {
    "my-network": {
        ...
    },
    "my-network2": {
        ...
    }
}
```
## 从网络中断开容器
在这一步中，你将学习如何在不停止容器的情况下将其从网络中移除。当你需要隔离容器或重新组织网络架构时，这非常有用。

### 任务

1. 使用 `docker network disconnect` 命令将 `container2` 从 `my-network` 中断开。
2. 验证 `container2` 不再连接到 `my-network`。

### 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 使用 `docker network disconnect` 命令将 `container2` 从 `my-network` 中移除。
- 使用 `docker network inspect` 命令验证断开连接的情况。

### 示例

将 `container2` 从 `my-network` 断开后，检查 `my-network` 时不应再看到 `container2`：

```
$ docker network inspect my-network
[
    {
        "Name": "my-network",
        ...
        "Containers": {
            "container1": {
                ...
            }
        },
        ...
    }
]
```
## 删除网络
在最后一步中，你将学习如何在不再需要 Docker 网络时将其删除。妥善清理未使用的资源是高效管理 Docker 环境的重要组成部分。

### 任务

1. 如果 `container2` 仍连接在 `my-network2` 上，请将其断开。
2. 使用 `docker network rm` 命令删除 `my-network2` 网络。
3. 验证 `my-network2` 已被成功删除。

### 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 在删除 `my-network2` 之前，确保没有任何容器连接到它。
- 使用 `docker network ls` 命令验证网络是否已删除。

### 示例

删除 `my-network2` 后，运行 `docker network ls` 时不应再看到它：

```
NETWORK ID     NAME         DRIVER    SCOPE
abcdef123456   my-network   bridge    local
ghijkl789012   bridge       bridge    local
mnopqr345678   host         host      local
stuvwx901234   none         null      local
```
## 总结
恭喜你完成了 Docker 网络基础挑战！你已经成功掌握了 Docker 网络的核心概念，包括：

1. 创建自定义 Docker 网络
2. 在特定网络中启动容器
3. 测试容器间的连通性
4. 将容器连接到多个网络
5. 从网络中断开容器
6. 删除 Docker 网络

这些技能构成了高效 Docker 网络管理的基础，在你处理更复杂的容器化应用时将发挥巨大作用。请记住，正确的网络配置对于容器隔离、安全以及服务间的高效通信至关重要。

在整个挑战过程中，你学习了如何为容器创建隔离环境、实现它们之间的通信以及动态管理网络连接。这些能力使你能够设计出能够适应不断变化需求的复杂网络架构。

随着你 Docker 学习之旅的继续，可以考虑探索更高级的网络主题，例如用于多主机设置的覆盖网络（overlay networks）、网络插件，以及 Docker 网络如何与 Kubernetes 等容器编排平台集成。你可能还想深入研究网络安全概念，例如如何使用网络策略（network policies）来控制容器间的流量。

继续练习这些技能，你将为在未来的项目中设计和管理复杂的容器网络架构做好充分准备。记住，高效的网络是构建可扩展、安全且高效的容器化应用的关键。