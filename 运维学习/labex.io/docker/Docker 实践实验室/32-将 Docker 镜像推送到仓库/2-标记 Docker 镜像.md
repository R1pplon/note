# 标记 Docker 镜像

下一步是为 Docker 镜像打标签，以便将其推送到容器仓库中。

龙之国王应执行以下命令为 `alpine` Docker 镜像打标签。

```bash
docker tag alpine < your-docker-hub-username > / < tag-name-for-image > :latest
```

在这一步中，你需要将 `alpine` 镜像标记为 `magical-alpine:latest`。

```bash
docker tag alpine < your-docker-hub-username > /magical-alpine:latest
```

如果你使用的是私有仓库，则可以使用 `docker tag <image-name> <registry-server-address>/<tag-name>:<version>` 为镜像打标签，以便将其推送到你的仓库服务器，例如：

```bash
docker tag enchanted-spells registry.dragonkingdom.com/enchanted-spells:latest
```
