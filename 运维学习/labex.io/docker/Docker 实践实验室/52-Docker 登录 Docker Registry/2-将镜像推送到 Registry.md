# 将镜像推送到 Registry

在这一步中，你将学习如何将 Docker 镜像推送到魔法海上贸易 registry。

1. 使用你的 `Docker Hub` 账户用户名对 `alpine` 镜像进行标记：

```bash
docker tag alpine < your-docker-username > /my_alpine:latest
```

如果你使用的是私有 registry，可以将镜像标记为以魔法海上贸易 registry 为前缀的形式：

```bash
docker tag alpine magical_maritime_trading.registry/myimage:latest
```

在接下来的步骤中，你只需要了解如何操作。如果你有一个真实的 registry 服务器，必须在终端中执行 `docker logout` 以从 `Docker Hub` 注销，并重新登录到你的 registry 服务器。

2. 将标记的镜像推送到 `Docker Hub`：

```bash
docker push < your-docker-username > /my_alpine:latest
```

如果你使用的是私有 registry，可以使用以下命令将标记的镜像推送到 registry：

```bash
# 请将 registry 服务器替换为你的真实 registry 服务器，否则命令将失败。
docker push magical_maritime_trading.registry/myimage:latest
```

3. 推送成功后，将显示镜像的摘要信息。

## 提示

`magical_maritime_trading.registry` 是一个虚拟的 registry 服务器地址，如果你有真实的 registry 服务器地址，请将其替换为真实的地址。
