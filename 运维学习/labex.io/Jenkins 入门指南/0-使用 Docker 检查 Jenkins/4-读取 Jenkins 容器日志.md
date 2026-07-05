# 读取 Jenkins 容器日志

在此步骤中，你将读取 Jenkins 容器的日志。当 Jenkins 无法启动或行为异常时，日志通常是首要检查的地方。

运行：

```bash
docker logs --tail 30 jenkins
```

输出应包含 Jenkins 的启动或运行时消息。你可能会看到 Jenkins 版本、插件消息或 HTTP 服务器启动日志。

具体的行内容可能有所不同，但该命令应返回正在运行的 `jenkins` 容器的最新日志。
