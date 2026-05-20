## 介绍

欢迎来到你的第一个 Docker 实验！在这个实验中，你将通过学习 Docker 迈出进入容器化世界的第一步。Docker 是一个用于开发、交付和运行应用程序的强大平台。

Docker 允许你将应用程序及其所有依赖项打包到一个称为「容器」的标准单元中。这使得在不同环境中一致地部署和运行应用程序变得更加容易。

**学习 Docker 的最佳方式是「动手实践」**。不要只是阅读本实验的内容 —— 请在 LabEx 环境中尝试每一个命令！这里是实验和学习的理想场所。

在本实验中，你将学习如何：

- 理解 Docker 的基本概念
- 运行你的第一个 Docker 容器
- 使用核心 Docker 命令
- 探索 Docker Hub

> **提示：** 本实验是 Docker 技能树（Docker Skill Tree）的一部分，这是一个结构化的知识体系。在每个步骤之后，系统都会验证你的操作，并为正确的响应授予技能点 💡。完成实验后，你可以访问 [Docker 技能树](https://labex.io/skilltrees/docker) 查看你积累的积分。

让我们开始 Docker 之旅吧！点击下方的**继续**或使用左侧的虚拟机图标开始实验。

## 理解 Docker 概念

在开始使用 Docker 之前，让我们先熟悉一些关键概念。如果它们起初看起来很复杂，请不要担心 —— 我们很快就会看到它们的实际应用！

1. **容器（Container）**：一个轻量级、独立且可执行的软件包，包含运行某个软件所需的一切。
2. **镜像（Image）**：可以将其视为容器的模板或蓝图。它包含了创建容器所需的所有指令。
3. **Docker Hub**：类似于 Docker 镜像的 GitHub —— 你可以在这里查找和分享容器镜像。
4. **Docker Engine**：在你的机器上运行和管理容器的核心技术。

这是一个简单的图表，可以帮助你直观地了解这些概念是如何协同工作的：

![](assets/1-你的第一个%20Docker%20实验/file-20260518142730883.png)

该图表显示：

- Docker Engine 运行容器
- 镜像用于创建容器
- Docker Hub 存储镜像
- Docker Engine 可以从 Docker Hub 拉取（pull）镜像，也可以向 Docker Hub 推送（push）镜像

理解这些关系将有助于你在后续过程中掌握 Docker 的工作原理。点击下方的**继续**进入下一步！

## 运行你的第一个容器

现在我们已经理解了基本概念，让我们使用 `hello-world` 镜像运行第一个 Docker 容器。这个简单的镜像旨在验证你的 Docker 安装是否正常工作，并向你介绍 Docker 的基础知识。

![Docker Hello World](https://labex.io/cdn-cgi/image/format=auto,quality=60,onerror=redirect/https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/docker/lab-the-first-docker-lab/assets/lab-hello-world-1-1.png)

要运行容器，请在终端中使用以下命令：

```bash
docker run hello-world
```

> **提示：** 点击任何代码块右下角的「Explain Code」，即可与 Labby AI 交流以获取详细解释。交流结束后，点击「Continue」返回实验。

让我们分解一下这个命令的作用：

1. `docker`：这是与 Docker Engine 交互的基础命令。
2. `run`：这个子命令告诉 Docker 创建并启动一个新容器。
3. `hello-world`：这是我们想要运行的镜像名称。

当你运行此命令时，后台会发生几件事：

1. Docker 检查本地是否存在 `hello-world` 镜像。
2. 如果不存在，它会自动从 Docker Hub 下载（或「拉取」）该镜像。
3. Docker 基于此镜像创建一个新容器。
4. 容器运行，显示一条消息，然后退出。

你应该会看到类似如下的输出：

```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

这段输出解释了 Docker 运行 `hello-world` 容器所经历的过程。让我们来拆解一下：

1. Docker 客户端（你运行的命令）联系了 Docker 守护进程（Docker daemon，在你机器上管理 Docker 的后台服务）。
2. 守护进程从 Docker Hub 拉取了「hello-world」镜像，因为本地没有该镜像。
3. 守护进程从该镜像创建了一个新容器并运行它。
4. 容器的输出被传回你的终端。

如果你现在还不完全理解这一切，请不要担心。随着我们的深入，这些概念会变得越来越清晰。

点击**继续**进入下一步。

## 理解 Docker 镜像

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

`hello-world` 镜像现在已经存储在你的本地系统中。这意味着如果你再次运行 `docker run hello-world`，Docker 将不需要从 Docker Hub 下载镜像，而是直接使用本地副本，从而加快运行速度。

如果你没有看到 hello-world 镜像，请不要担心！它可能为了节省空间而被自动删除了。你随时可以通过运行 `docker pull hello-world` 再次拉取它。

> 注意：LabEx 免费用户无法拉取镜像，因为免费虚拟机无法访问互联网。你可以[升级到付费计划](https://labex.io/pricing)以获得拉取镜像的权限。

点击**继续**进入下一步。

## 探索 Docker Hub

Docker Hub 是一个基于云的注册中心服务，Docker 用户和组织可以在这里存储和分发他们的 Docker 镜像。它就像是 Docker 镜像的 GitHub，作为一个中心仓库，你可以在这里查找、分享和管理 Docker 镜像。

让我们探索一下 Docker Hub：

1. 打开你本地的浏览器并访问 [https://hub.docker.com](https://hub.docker.com/)
2. 在顶部的搜索栏中输入「hello-world」并按回车键
3. 你会看到一个镜像列表。寻找官方的「hello-world」镜像（它应该带有一个「Official Image」徽章）
4. 点击「hello-world」镜像查看其详细信息

![Docker Hub 镜像搜索结果](https://labex.io/cdn-cgi/image/format=auto,quality=60,onerror=redirect/https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/docker/lab-the-first-docker-lab/assets/screenshot-20240820-F4JxR6K7@2x.png)

在镜像页面上，你可以看到：

- 镜像的描述
- 使用说明
- 镜像的拉取（下载）次数
- 可用的标签（版本）

当你运行 `docker run` 命令且本地没有该镜像时，Docker 就会去 Docker Hub 查找。这就是为什么即使你之前没有显式下载镜像，也能运行 hello-world 容器的原因。

关于 Docker Hub 的一些关键点：

1. **官方镜像（Official Images）**：这些镜像由 Docker 官方策划，通常维护良好且文档齐全。它们是初学者的理想选择。
2. **标签（Tags）**：镜像可以有多个版本，称为标签。例如，你可能会看到「latest」、「1.0」、「2.1」等标签。当你没有指定标签时（就像我们运行 `docker run hello-world` 那样），Docker 会默认你想要「latest」标签。
3. **拉取命令（Pull Command）**：在每个镜像的页面上，你都会看到一个「Pull Command」。这是你在不运行容器的情况下手动下载镜像所使用的命令。例如：`docker pull hello-world`
4. **Dockerfile**：Docker Hub 上的许多镜像都会提供其 Dockerfile 的链接，这是用于构建镜像的脚本。如果你想了解镜像是如何创建的，这会非常有帮助。

探索 Docker Hub 并了解如何查找和使用镜像是使用 Docker 的一项至关重要的技能。随着你的进步，你会发现自己经常在 Docker Hub 上搜索适合你需求的镜像。

这是你第一个 Docker 实验的最后一步！点击**继续**来检查你掌握的所有技能。

## 总结

恭喜你！你已经完成了第一个 Docker 实验，并迈出了进入容器化世界的第一步！你已经学会了如何：

- 理解 Docker 的基本概念
- 使用 `hello-world` 镜像运行你的第一个容器
- 查看并理解系统上的 Docker 镜像
- 浏览 Docker Hub 以查找和了解镜像

这只是你 Docker 之旅的开始！你已经看到了容器化是多么强大，即使是简单的例子也是如此。准备好将你的技能提升到新的水平了吗？

[**LabEx Pro**](https://labex.io/pricing?utm_source=labby) 将为你开启 Docker 学习的新世界，包括：

![LabEx Pro](https://labex.io/cdn-cgi/image/format=auto,quality=60,onerror=redirect/https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/docker/lab-the-first-docker-lab/assets/labex-pro.png)

> 如果你已经升级到 LabEx Pro，请忽略此推广横幅，它不会再次显示。

- **高级 Docker 实验**：学习容器编排、多容器应用以及生产环境部署策略
- **真实世界项目**：在实际开发场景中积累 Docker 的实践经验
- **专家支持**：随时从 Docker 专业人士那里获得帮助
- **全面的技能树**：为 DevOps 和云计算领域的职业生涯打下坚实基础

继续通过更多免费实验进行你的 Docker 冒险，或者[升级到 LabEx Pro](https://labex.io/pricing?utm_source=labby) 以加速成为 Docker 专家的进程！