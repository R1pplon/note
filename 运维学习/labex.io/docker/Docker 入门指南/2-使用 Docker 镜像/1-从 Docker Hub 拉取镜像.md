# 从 Docker Hub 拉取镜像

Docker Hub 是一个公共的 Docker 镜像仓库，类似于代码的 GitHub。在这里，你可以找到许多流行软件应用程序和操作系统的预构建镜像。让我们从拉取（下载）官方的 Nginx 镜像开始。

在你的系统上打开一个终端。你应该会看到一个类似以下的提示符：

```
labex:project/ $
```

现在，让我们拉取 Nginx 镜像。输入以下命令并按回车：

```bash
docker pull nginx
```

这条命令告诉 Docker 从 Docker Hub 下载最新版本的 Nginx 镜像。你应该会看到类似以下的输出：

```
Using default tag: latest
latest: Pulling from library/nginx
5040bd298390: Pull complete
d7a91cdb22f0: Pull complete
9cac4850e5df: Pull complete
Digest: sha256:33ff28a2763feccc1e1071a97960b7fef714d6e17e2d0ff573b74825d0049303
Status: Downloaded newer image for nginx:latest
```

让我们分解一下这里发生了什么：

1. "Using default tag: latest" - 当你不指定版本时，Docker 会默认拉取最新版本。
2. 接下来的几行显示 Docker 正在下载镜像的不同“层”。每一层代表一组文件系统的更改。
3. "Digest" 是这个镜像版本的唯一标识符。
4. 最后一行确认镜像已成功下载。

现在我们已经下载了镜像，让我们验证它是否在我们的系统中。我们可以通过列出 Docker 本地所有的镜像来做到这一点：

```bash
docker images
```

你应该会看到类似以下的输出：

```
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
nginx         latest    605c77e624dd   2 weeks ago    141MB
```

这告诉我们：

- REPOSITORY: 镜像的名称（nginx）
- TAG: 镜像的版本（latest）
- IMAGE ID: 这个镜像的唯一标识符
- CREATED: 这个镜像版本的创建时间
- SIZE: 镜像占用的磁盘空间

如果具体数字不同，不用担心——重要的是你看到了 nginx 的条目。

如果你好奇系统中还有哪些其他镜像，你可能会看到 "jenkins/jenkins" 和 "gcr.io/k8s-minikube/kicbase" 的条目。这些是预安装的镜像，我们不会在本实验中使用它们。
