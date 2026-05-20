# 理解 Docker 镜像

既然我们已经运行了第一个容器，让我们更详细地探索一下 Docker 镜像。记住，镜像就像是容器的蓝图或模板。它包含了创建容器所需的所有指令。

要查看本地系统上可用的镜像，请使用以下命令：

```bash
docker images
```

你应该会看到类似如下的输出：

```
REPOSITORY          TAG       IMAGE ID       CREATED         SIZE
hello-world         latest    feb5d9fea6a5   2 weeks ago     13.3kB
```

让我们分解每一列的含义：

- `REPOSITORY`：镜像的名称。在本例中是「hello-world」。
- `TAG`：镜像的版本。如果你不指定版本，默认标签是「latest」。
- `IMAGE ID`：镜像的唯一标识符。当你需要引用特定镜像时，这非常有用。
- `CREATED`：镜像创建的时间。这有助于你了解是否拥有最新版本。
- `SIZE`：镜像在磁盘上的大小。Docker 镜像被设计得非常轻量，这就是为什么 hello-world 镜像只有 13.3kB。

`hello-world` 镜像现在已经存储在你的本地系统中。这意味着如果你再次运行 `docker run hello-world`，Docker 将不需要从 Docker Hub 下载镜像，而是直接使用本地副本，从而加快运行速度。

如果你没有看到 hello-world 镜像，请不要担心！它可能为了节省空间而被自动删除了。你随时可以通过运行 `docker pull hello-world` 再次拉取它。

> 注意：LabEx 免费用户无法拉取镜像，因为免费虚拟机无法访问互联网。你可以[升级到付费计划](https://labex.io/pricing)以获得拉取镜像的权限。

点击**继续**进入下一步。
