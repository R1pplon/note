# 探索 Docker 容器端口

在这一步中，你将首先运行一个 Docker 容器，然后探索其暴露的端口。

首先，让我们运行一个简单的 Nginx 容器：

```bash
docker run -d -p 8080:80 --name my-nginx nginx
```

接下来，你将检查容器正在监听的端口：

```bash
docker port my-nginx
```

预期的输出应显示容器端口到主机端口的映射。

```bash
80/tcp - > 0.0.0.0:8080
80/tcp - > :::8080
```
