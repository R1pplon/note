# 创建简单的 Docker 网络

在这一步中，你将创建一个简单的 Docker 网络，并将两个容器连接到该网络。你将学习如何创建 Docker 网络以及如何将容器连接到该网络。

## 目标

创建一个名为 `my-network` 的 Docker 网络，并运行两个分别名为 `nginx-container` 和 `alpine-container` 的容器，将它们连接到该网络。

## 结果示例

以下是你完成本挑战后应达到的效果示例：

1. 创建一个名为 `my-network` 的 Docker 网络。

   ```bash
   docker network ls | grep my
   ```

   示例输出：

   ```plaintext
   4beeab11f8b9   my-network   bridge    local
   ```

2. 运行一个名为 `nginx-container` 的 Nginx 容器，并将其连接到 `my-network`。

   ```bash
   docker inspect nginx-container | grep -i my-network -C 8
   ```

   示例输出：

   ```plaintext
   "ExecIDs": null,
   "HostConfig": {
       "Binds": null,
       "ContainerIDFile": "",
       "LogConfig": {
           "Type": "json-file",
           "Config": {}
       },
       "NetworkMode": "my-network",
       "PortBindings": {},
       "RestartPolicy": {
           "Name": "no",
           "MaximumRetryCount": 0
       },
   ...
   ```

3. 运行一个名为 `alpine-container` 的 Alpine 容器，并将其连接到 `my-network`。

   ```bash
   docker ps | grep alpine
   ```

   示例输出：

   ```plaintext
   7d2dbb6abba9   alpine    "/bin/sh"                55 seconds ago   Up 54 seconds             alpine-container
   ```

   ```bash
   docker inspect alpine-container | grep my-network
   ```

   示例输出：

   ```plaintext
   "NetworkMode": "my-network",
       "my-network": {
   ```

4. 通过从一个容器 ping 另一个容器，验证它们是否可以相互通信。

   示例输出：

   ```plaintext
   PING nginx-container (172.18.0.2): 56 data bytes
   64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.159 ms
   64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.088 ms
   64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.092 ms
   ^C
   --- nginx-container ping statistics ---
   3 packets transmitted, 3 packets received, 0% packet loss
   round-trip min/avg/max = 0.088/0.113/0.159 ms
   ```
