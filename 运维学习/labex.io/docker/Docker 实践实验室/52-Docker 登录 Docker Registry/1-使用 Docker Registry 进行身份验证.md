# 使用 Docker Registry 进行身份验证

在这一步中，你将学习如何使用 Docker CLI 与 registry 进行身份验证。如果你还没有 Docker Hub 账户，请访问 [Docker Hub 网站](https://hub.docker.com/) 并注册一个免费账户。如果你使用的是私有 registry，则可以使用 `docker login <registry-server-address>` 登录到你的 registry 服务器。

1. 使用 `docker login` 命令与魔法海上贸易 registry 进行身份验证：

```bash
docker login
```

2. 运行 `docker login` 命令后，系统会提示你输入用户名和密码以完成身份验证。
