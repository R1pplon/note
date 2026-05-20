# 探索 Docker Hub

Docker Hub 是一个基于云的注册中心服务，Docker 用户和组织可以在这里存储和分发他们的 Docker 镜像。它就像是 Docker 镜像的 GitHub，作为一个中心仓库，你可以在这里查找、分享和管理 Docker 镜像。

让我们探索一下 Docker Hub：

1. 打开你本地的浏览器并访问 <https://hub.docker.com>
2. 在顶部的搜索栏中输入「hello-world」并按回车键
3. 你会看到一个镜像列表。寻找官方的「hello-world」镜像（它应该带有一个「Official Image」徽章）
4. 点击「hello-world」镜像查看其详细信息

![Docker Hub 镜像搜索结果](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/docker/lab-the-first-docker-lab/zh/../assets/screenshot-20240820-F4JxR6K7@2x.png)

在镜像页面上，你可以看到：

- 镜像的描述
- 使用说明
- 镜像的拉取（下载）次数
- 可用的标签（版本）

当你运行 `docker run` 命令且本地没有该镜像时，Docker 就会去 Docker Hub 查找。这就是为什么即使你之前没有显式下载镜像，也能运行 hello-world 容器的原因。

关于 Docker Hub 的一些关键点：

1. **官方镜像（Official Images）**：这些镜像由 Docker 官方策划，通常维护良好且文档齐全。它们是初学者的理想选择。
2. **标签（Tags）**：镜像可以有多个版本，称为标签。例如，你可能会看到「latest」、「1.0」、「2.1」等标签。当你没有指定标签时（就像我们运行 `docker run hello-world` 那样），Docker 会默认你想要「latest」标签。
3. **拉取命令（Pull Command）**：在每个镜像的页面上，你都会看到一个「Pull Command」。这是你在不运行容器的情况下手动下载镜像所使用的命令。例如：`docker pull hello-world`
4. **Dockerfile**：Docker Hub 上的许多镜像都会提供其 Dockerfile 的链接，这是用于构建镜像的脚本。如果你想了解镜像是如何创建的，这会非常有帮助。

探索 Docker Hub 并了解如何查找和使用镜像是使用 Docker 的一项至关重要的技能。随着你的进步，你会发现自己经常在 Docker Hub 上搜索适合你需求的镜像。

这是你第一个 Docker 实验的最后一步！点击**继续**来检查你掌握的所有技能。
