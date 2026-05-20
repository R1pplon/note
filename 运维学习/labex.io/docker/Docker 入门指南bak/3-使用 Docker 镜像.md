## 介绍

在本实验中，我们将探索 Docker 镜像，这是创建和运行容器的基础。我们将学习如何从 Docker Hub 拉取镜像、使用不同版本的镜像运行容器、列出和删除镜像、理解镜像层、搜索镜像以及执行基本的镜像标记操作。通过这个动手实践，你将掌握有效使用 Docker 镜像的基本技能。如果你是 Docker 新手，不用担心——我们将通过详细的解释逐步引导你完成每个步骤。

## 从 Docker Hub 拉取镜像

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

如果你好奇系统中还有哪些其他镜像，你可能会看到 "jenkins/jenkins" 和 "[gcr.io/k8s-minikube/kicbase](http://gcr.io/k8s-minikube/kicbase)" 的条目。这些是预安装的镜像，我们不会在本实验中使用它们。

## 运行不同版本的镜像

Docker 允许你通过使用标签（tags）来运行特定版本的镜像。标签就像是镜像特定版本的别名。让我们以 Python 镜像为例来探索这个概念。

首先，拉取最新的 Python 镜像：

```bash
docker pull python
```

你会看到类似于我们拉取 Nginx 镜像时的输出。这表示正在下载最新版本的 Python。

现在，让我们拉取一个特定版本的 Python，比如 3.7 版本：

```bash
docker pull python:3.7
```

注意我们在 `python` 后面添加了 `:3.7`。这告诉 Docker 拉取特定的 3.7 版本，而不是最新版本。

让我们列出我们的 Python 镜像，看看不同的版本：

```bash
docker images python
```

你应该会看到类似以下的输出：

```
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
python       3.7       1f1a7b570fbd   2 weeks ago    907MB
python       latest    98ccd1643c71   2 weeks ago    920MB
```

现在我们有两个 Python 镜像：一个标记为 `latest`（实际上是 Python 3.9 或 3.10，具体取决于你进行本实验的时间），另一个标记为 `3.7`。

让我们使用这些不同的版本来运行容器，看看它们的区别：

```bash
docker run --rm python:latest python --version
```

这条命令做了以下几件事：

1. `docker run` 创建并启动一个新的容器
2. `--rm` 告诉 Docker 在容器退出后将其删除
3. `python:latest` 指定要使用的镜像
4. `python --version` 是在容器内运行的命令

你应该会在输出中看到最新的 Python 版本。

现在让我们对 Python 3.7 做同样的操作：

```bash
docker run python:3.7 python --version
```

这次，你应该会在输出中看到 `Python 3.7.x`，其中 `x` 是 Python 3.7 的最新补丁版本。

这些命令展示了如何通过使用不同的镜像标签来运行同一软件的不同版本。当你需要运行需要特定版本 Python（或其他软件）的应用程序时，这非常有用。

## 列出和删除镜像

在使用 Docker 的过程中，你会逐渐积累许多镜像。了解如何管理这些镜像非常重要，包括如何列出它们以及删除不再需要的镜像。

让我们从列出系统中的所有镜像开始：

```bash
docker images
```

你应该会看到你到目前为止拉取的所有镜像列表，包括 Nginx 和 Python 镜像。

现在，假设我们想要删除 Python 3.7 镜像以释放一些空间。我们可以使用 `docker rmi` 命令（rmi 代表 "remove image"）：

```bash
docker rmi python:3.7
```

如果命令成功执行，你会看到类似以下的输出：

```
Untagged: python:3.7
Untagged: python@sha256:1f93c63...
Deleted: sha256:1f1a7b57...
Deleted: sha256:8c75ecde...
...
```

然而，你可能会看到类似以下的错误信息：

```
Error response from daemon: conflict: unable to remove repository reference "python:3.7" (must force) - container <container_id> is using its referenced image <image_id>
```

如果存在从该镜像创建的容器（无论是正在运行还是已停止），就会出现此错误。Docker 会阻止你删除正在使用的镜像，以维护系统的完整性。

要解决这个问题，我们需要先删除使用该镜像的所有容器。让我们列出所有容器（包括已停止的）：

```bash
docker ps -a
```

查找任何从 `python:3.7` 镜像创建的容器。如果找到，使用 `docker rm` 命令删除它们：

```bash
docker rm <container_id>
```

将 `<container_id>` 替换为你想要删除的容器的实际 ID。

现在再次尝试删除镜像：

```bash
docker rmi python:3.7
```

这次应该会成功。

让我们通过再次列出 Python 镜像来验证镜像是否已被删除：

```bash
docker images python
```

你应该不会再在列表中看到 Python 3.7 镜像。

## 理解镜像层

Docker 镜像是使用分层文件系统构建的。每一层代表一组文件系统的更改。这种分层方法使 Docker 在存储和网络使用上更加高效。让我们来探索这个概念。

首先，让我们检查之前拉取的 Nginx 镜像的层：

```bash
docker inspect --format='{{.RootFS.Layers}}' nginx
```

你会看到类似以下的输出：

```
[sha256:2edcec3590a4ec7f40cf0743c15d78fb39d8326bc029073b41ef9727da6c851f sha256:e379e8aedd4d72bb4c529a4ca07a4e4d230b5a1d3f7a61bc80179e8f02421ad8 sha256:b5357ce95c68acd9c9672ec76e3b2a2ff3f8f62a2bcc1866b8811572f4d409af]
```

这些长字符串（称为 SHA256 哈希值）中的每一个都代表镜像中的一个层。每一层对应于用于构建镜像的 Dockerfile 中的一条命令。

为了更好地理解层，让我们创建一个简单的自定义镜像。首先，在当前目录下创建一个名为 `Dockerfile` 的新文件：

```bash
nano Dockerfile
```

在这个文件中，添加以下内容：

```dockerfile
FROM nginx
RUN echo "Hello from custom layer" > /usr/share/nginx/html/hello.html
```

这个 Dockerfile 做了两件事：

1. 它从我们之前拉取的 Nginx 镜像开始（`FROM nginx`）
2. 它向镜像中添加一个新文件（`RUN echo...`）

保存并退出文件（在 nano 中，你可以按 Ctrl+X，然后按 Y，再按回车）。

现在让我们构建这个镜像：

```bash
docker build -t custom-nginx .
```

示例输出：

```
Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM nginx
 ---> 5ef79149e0ec
Step 2/2 : RUN echo "Hello from custom layer" > /usr/share/nginx/html/hello.html
 ---> Running in 2fa43e649234
Removing intermediate container 2fa43e649234
 ---> 73b62663b5c3
Successfully built 73b62663b5c3
Successfully tagged custom-nginx:latest
```

这条命令基于我们的 Dockerfile 构建了一个新镜像，并将其标记为 `custom-nginx`。末尾的 `.` 告诉 Docker 在当前目录中查找 Dockerfile。

现在，让我们检查自定义镜像的层：

```bash
docker inspect --format='{{.RootFS.Layers}}' custom-nginx
```

你会注意到这个镜像比原始的 Nginx 镜像多了一层。这额外的层代表了我们 `RUN` 命令所做的更改。

理解层非常重要，因为：

1. 层会被缓存，从而加速类似镜像的构建
2. 层在镜像之间共享，节省磁盘空间
3. 在推送或拉取镜像时，只有更改的层需要传输

## 在 Docker Hub 上搜索镜像

Docker Hub 托管了大量的镜像集合。虽然你可以在 Docker Hub 网站上搜索镜像，但 Docker 也提供了一个命令行工具，可以直接从终端搜索镜像。

让我们从搜索 Nginx 镜像开始：

```bash
docker search nginx
```

这将返回与 Nginx 相关的镜像列表。输出包括以下几列：

- NAME: 镜像的名称
- DESCRIPTION: 镜像的简要描述
- STARS: 镜像在 Docker Hub 上的星标数量（表示受欢迎程度）
- OFFICIAL: 该镜像是否为 Docker 维护的官方镜像
- AUTOMATED: 该镜像是否从 GitHub 仓库自动构建

例如，你可能会看到类似以下的内容：

```
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
nginx                             Official build of Nginx.                        15763     [OK]
jwilder/nginx-proxy               Automated Nginx reverse proxy for docker c...   2088                 [OK]
...
```

官方的 Nginx 镜像通常位于列表的顶部。

现在，让我们尝试搜索特定版本的 Python：

```bash
docker search python:3.8
```

你会注意到这个搜索并不像你预期的那样工作。Docker 搜索不支持搜索特定的标签（如 3.8）。相反，它会搜索名称或描述中包含 "python:3.8" 的镜像。

要找到特定版本的镜像，通常更好的方法是：

1. 搜索通用的镜像名称（例如 `docker search python`）
2. 访问 Docker Hub 网站以获取更详细的信息
3. 使用 `docker pull` 下载镜像，然后在本地检查

记住，`docker search` 是快速查找镜像的方式，但对于更详细的信息，Docker Hub 网站通常更有用。

## 保存和加载镜像

Docker 允许你将镜像保存为 tar 文件，并在以后加载它们。这对于在不使用镜像仓库的情况下在系统之间传输镜像，或者备份镜像非常有用。

让我们从将 Nginx 镜像保存到文件开始：

```bash
docker save nginx > nginx.tar
```

这条命令将 Nginx 镜像保存到当前目录下名为 `nginx.tar` 的文件中。`>` 符号用于将 `docker save` 命令的输出重定向到文件。

你可以通过列出当前目录的内容来验证文件是否已创建：

```bash
ls -lh nginx.tar
```

你应该会看到列出的 `nginx.tar` 文件及其大小（应该超过 100MB）。

现在，让我们从系统中删除 Nginx 镜像，以模拟将镜像传输到一个没有该镜像的系统：

```bash
docker rmi nginx
```

验证镜像是否已被删除：

```bash
docker images nginx
```

你应该看不到任何结果，这表明 Nginx 镜像已从你的系统中移除。

现在，让我们从 tar 文件中加载镜像：

```bash
docker load < nginx.tar
```

`<` 符号用于将 `nginx.tar` 文件的内容重定向为 `docker load` 命令的输入。

加载完成后，验证 Nginx 镜像是否已恢复：

```bash
docker images nginx
```

你应该会再次在列表中看到 Nginx 镜像，就像我们删除它之前一样。

这种保存和加载镜像的过程在以下情况下非常有用：

- 将镜像传输到没有互联网访问的系统
- 备份特定版本的镜像
- 在不使用镜像仓库的情况下与他人共享自定义镜像

## 镜像标记基础

标记（Tagging）是为 Docker 镜像创建别名的一种方式。它通常用于版本控制和镜像组织。让我们探索如何标记镜像。

首先，为我们的 Nginx 镜像创建一个新标记：

```bash
docker tag nginx:latest my-nginx:v1
```

这条命令创建了一个新标记 `my-nginx:v1`，它指向与 `nginx:latest` 相同的镜像。以下是每个部分的含义：

- `nginx:latest` 是源镜像和标记
- `my-nginx` 是我们创建的新镜像名称
- `v1` 是我们分配的新标记

现在，列出你的镜像以查看新标记：

```bash
docker images
```

你应该会在列表中看到 `nginx:latest` 和 `my-nginx:v1`。注意它们具有相同的 Image ID——这是因为它们实际上是同一个镜像，只是名称不同。

你可以使用这个新标记来运行一个容器：

```bash
docker run -d --name my-nginx-container my-nginx:v1
```

这条命令做了以下事情：

- `-d` 以分离模式（后台运行）运行容器
- `--name my-nginx-container` 为我们的新容器命名
- `my-nginx:v1` 是用于创建容器的镜像和标记

验证容器是否正在运行：

```bash
docker ps
```

你应该会在运行中的容器列表中看到你的容器。

标记有以下几个用途：

1. 版本控制：你可以为镜像标记版本号（v1、v2 等）
2. 环境分离：你可以为不同环境（开发、测试、生产）标记镜像
3. 可读性：自定义标记可以使镜像用途更清晰

记住，标记只是别名——它们不会创建新镜像，只是为现有镜像创建新的名称。

## 总结

在本实验中，我们探索了与 Docker 镜像相关的各种操作。我们学习了如何：

1. 从 Docker Hub 拉取镜像
2. 使用不同版本的镜像运行容器
3. 列出和删除镜像
4. 理解镜像层
5. 在 Docker Hub 上搜索镜像
6. 保存和加载镜像
7. 执行基本的镜像标记

这些技能为你有效管理项目中的 Docker 镜像奠定了基础。随着你在 Docker 领域的深入学习，你会发现这些操作对于构建和部署容器化应用程序至关重要。

记住，Docker 镜像是 Docker 运行的核心。它们提供了一种一致、可移植且高效的方式来打包和分发应用程序。通过掌握这些镜像操作，你已经在成为 Docker 高手的道路上迈出了重要一步。

继续练习这些命令并探索不同的镜像。你使用 Docker 的次数越多，就会越熟练和得心应手。祝你玩得开心！