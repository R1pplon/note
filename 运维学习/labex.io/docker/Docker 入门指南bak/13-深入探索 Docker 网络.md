## 介绍
在本实验中，我们将在 Docker 网络基础之上，进一步探索高级 Docker 网络概念。我们将深入研究三种主要的网络模式：桥接（Bridge）、主机（Host）和无网络（None）。你还将学习如何创建自定义网络、跨不同网络连接容器，并理解每种网络模式对容器通信和隔离的影响。通过这些动手实践，你将为 Docker 网络实践打下坚实的基础。
## 自定义桥接网络
虽然 Docker 提供了一个默认的桥接网络，但创建自定义桥接网络可以提供更好的隔离性，并能更精细地控制容器间的通信。

1. 首先，让我们查看当前的 Docker 网络列表：

```bash
docker network ls
```

你应该会看到类似以下的内容：

```
NETWORK ID     NAME      DRIVER    SCOPE
296d1b460b17   bridge    bridge    local
91199fc6ad2e   host      host      local
1078d2c781b6   none      null      local
```

2. 现在，让我们创建一个自定义桥接网络：

```bash
docker network create my-custom-bridge
```

3. 再次列出所有 Docker 网络以验证创建是否成功：

```bash
docker network ls
```

你现在应该能在输出中看到 `my-custom-bridge`：

```
NETWORK ID     NAME               DRIVER    SCOPE
296d1b460b17   bridge             bridge    local
91199fc6ad2e   host               host      local
7215f99d0080   my-custom-bridge   bridge    local
1078d2c781b6   none               null      local
```

4. 在自定义桥接网络上启动两个容器并安装 ping 工具：

```bash
docker run --network=my-custom-bridge --name container1 -d nginx
docker run --network=my-custom-bridge --name container2 -d nginx
```

现在，让我们在两个容器中都安装 ping 工具。我们需要这样做是因为默认的 Nginx 镜像不包含 ping：

```bash
docker exec container1 apt-get update && docker exec container1 apt-get install -y iputils-ping
docker exec container2 apt-get update && docker exec container2 apt-get install -y iputils-ping
```

5. 测试容器间的通信：

```bash
docker exec container1 ping -c 4 container2
```

你应该能看到成功的 ping 响应，这证明了在同一个自定义桥接网络上的容器可以使用容器名称进行通信。这是因为 Docker 内置的 DNS 会在同一个网络内将容器名称解析为它们的 IP 地址。

如果你没有看到成功的 ping 结果，请确保两个容器都在运行（使用 `docker ps` 查看），并且你已正确安装了 ping 工具。
## 跨网络连接容器
Docker 允许将一个容器连接到多个网络，从而实现不同网络间容器的通信。当你想要隔离某些容器，但仍允许它们之间进行特定通信时，这非常有用。

1. 首先，让我们创建第二个自定义桥接网络：

```bash
docker network create my-second-bridge
```

2. 验证网络创建：

```bash
docker network ls
```

你现在应该能看到两个自定义网络：

```
NETWORK ID     NAME               DRIVER    SCOPE
296d1b460b17   bridge             bridge    local
91199fc6ad2e   host               host      local
7215f99d0080   my-custom-bridge   bridge    local
8a15f99d0081   my-second-bridge   bridge    local
1078d2c781b6   none               null      local
```

3. 将 `container2` 连接到新网络：

```bash
docker network connect my-second-bridge container2
```

此命令将 `container2` 添加到 `my-second-bridge` 网络，同时保持它与 `my-custom-bridge` 的连接。

4. 在第二个网络上创建一个新容器：

```bash
docker run --network=my-second-bridge --name container3 -d nginx
docker exec container3 apt-get update && docker exec container3 apt-get install -y iputils-ping
```

5. 测试所有容器间的通信：

```bash
docker exec container1 ping -c 2 container2
docker exec container1 ping -c 2 container3
docker exec container2 ping -c 2 container3
```

注意结果：

- `container1` 可以与 `container2` 通信（它们在同一个网络中）。
- `container1` 无法与 `container3` 通信（它们在不同的网络中）。
- `container2` 可以同时与 `container1` 和 `container3` 通信（它连接到了这两个网络）。

如果 `container1` 能够 ping 通 `container3`，说明你的网络隔离出现了问题。
## 主机网络模式
主机网络模式（Host network mode）移除了容器与 Docker 宿主机之间的网络隔离，允许容器直接使用宿主机的网络。这对于追求极致性能很有帮助，但由于降低了隔离性，也会带来安全隐患。

1. 使用主机网络模式创建一个容器：

```bash
docker run --network host --name host-container -d nginx
```

2. 验证当前网络列表：

```bash
docker network ls
```

你不会看到为此容器创建的新网络，因为它直接使用宿主机的网络。

3. 验证容器正在使用主机网络：

```bash
docker inspect --format '{{.HostConfig.NetworkMode}}' host-container
```

输出应该是 `host`。

4. 尝试从宿主机访问 Nginx 默认页面：

```bash
curl localhost:80
```

你应该能看到 Nginx 的欢迎页面。这是因为容器使用了宿主机的网络，而 Nginx 正在监听宿主机网络接口的 80 端口。

注意：如果你的宿主机 80 端口已经运行了其他服务，此步骤可能会失败。在这种情况下，你需要先停止已有的服务。
## 无网络模式
「无网络」模式（None network mode）创建的容器没有网络接口，使其与网络完全隔离。这对于追求极致的安全隔离非常有用，但也意味着容器完全无法通过网络进行通信。

1. 创建一个没有网络的容器：

```bash
docker run --network none --name isolated-container -d alpine sleep infinity
```

2. 验证当前网络列表：

```bash
docker network ls
```

你不会看到为此容器创建的新网络，因为它没有连接到任何网络。

3. 验证容器没有网络接口：

```bash
docker exec isolated-container ip addr
```

你应该只能看到回环接口（lo）。不会有 eth0 或其他网络接口。

4. 尝试从隔离的容器中 ping Google：

```bash
docker exec isolated-container ping -c 2 google.com
```

这应该会失败并提示「Network is unreachable」错误，因为该容器没有网络访问权限。
## 网络别名与服务发现
Docker 网络支持使用网络别名（Network Aliases）进行服务发现，这对于构建具有弹性、可扩展的应用非常有用。此功能允许通过同一个 DNS 名称访问多个容器，从而实现基础的负载均衡。

1. 为此练习创建一个新的桥接网络：

```bash
docker network create service-network
```

2. 验证网络创建：

```bash
docker network ls
```

你应该能在列表中看到 `service-network`。

3. 创建两个具有相同网络别名的容器：

```bash
docker run -d --network service-network --network-alias myservice --name service1 nginx
docker run -d --network service-network --network-alias myservice --name service2 nginx
```

4. 创建一个客户端容器并使用 `nslookup` 解析服务：

```bash
docker run --rm --network service-network appropriate/curl nslookup myservice
```

你应该能看到返回了两个容器的 IP 地址，这证明了 Docker 的嵌入式 DNS 服务器正在这两个容器之间进行负载均衡。

5. 测试多次访问该服务：

```bash
for i in {1..4}; do docker run --rm --network service-network appropriate/curl ping -c 1 myservice; done
```

你应该能看到来自两个容器的响应，展示了基础的负载均衡。Docker DNS 服务器在解析 `myservice` 时会在两个 IP 地址之间交替切换。

```plaintext
PING myservice (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.106 ms

--- myservice ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.106/0.106/0.106 ms
PING myservice (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.119 ms

--- myservice ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.119/0.119/0.119 ms
PING myservice (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.097 ms

--- myservice ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.097/0.097/0.097 ms
PING myservice (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.140 ms

--- myservice ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.140/0.140/0.140 ms
```
## 总结
在这个高级 Docker 网络实验中，我们探索了几个关键概念：

1. 自定义桥接网络，用于增强容器的隔离与通信。
2. 跨多个网络连接容器。
3. 主机网络模式，用于直接访问宿主机的网络栈。
4. 无网络模式，用于实现完全的网络隔离。
5. 网络别名与服务发现，用于构建可扩展的应用。

Docker 的这些高级网络功能为你设计复杂的多容器应用提供了强大的工具，使你能够精确控制容器间的通信与隔离。理解这些概念对于设计高效、安全且可扩展的容器化应用至关重要。

请记住，虽然 Docker 网络提供了极大的灵活性，但在设计容器网络架构时，考虑安全影响同样重要。请始终遵循最小权限原则，仅暴露必要的端口和服务。

随着你继续深入使用 Docker，你会发现这些网络概念在编排复杂应用和微服务架构时具有不可估量的价值。通过定期练习这些概念，你将能够熟练且有效地管理 Docker 网络。