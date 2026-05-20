# 将端口映射到特定的 IP 地址

在这一步中，你将学习如何使用 `docker run` 命令将端口映射到特定的 IP 地址。

## 目标

为 `my-nginx2` 容器将端口 `8080` 映射到 IP 地址 `0.0.0.0`。

## 结果示例

这是你在本步骤结束时应能完成的示例：

1. 打开终端或命令提示符。
2. 为 `my-nginx2` 容器将端口 `8080` 映射到 IP 地址 `0.0.0.0`。

```
labex:~/ $ docker ps | grep my-nginx2
9981ef76a171   nginx     "/docker-entrypoint.…"   37 seconds ago   Up 37 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   my-nginx2
```
