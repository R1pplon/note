# 使用 Docker 网络驱动

在这一步中，你将学习如何使用 Docker 网络驱动。你将使用驱动程序为你的容器创建一个网络。

## 目标

使用 Docker 网络驱动为你的容器创建一个网络。

## 结果示例

以下是你完成本挑战后应达到的效果示例：

1. 使用 bridge 驱动创建一个名为 `my-network2` 的网络。

   ```bash
   docker network ls | grep -E "network2\s+br"
   ```

   ```plaintext
   ce89d0c3ea11   my-network2   bridge    local
   ```

2. 运行一个名为 `alpine-container2` 的 Alpine 容器，并将其连接到 `my-network2`。

   ```bash
   docker ps | grep alpine-container2
   ```

   示例输出：

   ```plaintext
   d3223638a9b9   alpine    "/bin/sh"                4 minutes ago    Up 4 minutes              alpine-container2
   ```

   ```bash
   docker inspect alpine-container2 | grep my-network2
   ```

   示例输出：

   ```plaintext
               "NetworkMode": "my-network2",
                   "my-network2": {
   ```

3. 运行一个名为 `nginx-container2` 的 Nginx 容器，并将其连接到 `my-network2`。

   ```bash
   docker ps | grep nginx-container2
   ```

   示例输出：

   ```plaintext
   524af103e048   nginx     "/docker-entrypoint.…"   49 seconds ago   Up 49 seconds   80/tcp    nginx-container2
   ```

   ```bash
   docker inspect nginx-container2 | grep my-network2
   ```

   示例输出：

   ```plaintext
   "NetworkMode": "my-network2",
       "my-network2": {
   ```

4. 验证该容器是否可以与同一网络上的另一个容器通信。

   示例输出：

   ```plaintext
   PING nginx-container2 (172.20.0.3): 56 data bytes
   64 bytes from 172.20.0.3: seq=0 ttl=64 time=0.199 ms
   64 bytes from 172.20.0.3: seq=1 ttl=64 time=0.090 ms
   64 bytes from 172.20.0.3: seq=2 ttl=64 time=0.086 ms
   ^C
   --- nginx-container2 ping statistics ---
   3 packets transmitted, 3 packets received, 0% packet loss
   round-trip min/avg/max = 0.086/0.125/0.199 ms
   ```
