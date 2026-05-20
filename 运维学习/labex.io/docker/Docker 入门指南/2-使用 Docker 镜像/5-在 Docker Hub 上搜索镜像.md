# 在 Docker Hub 上搜索镜像

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

1. 搜索通用的镜像名称（例如 `docker search python`）
2. 访问 Docker Hub 网站以获取更详细的信息
3. 使用 `docker pull` 下载镜像，然后在本地检查

记住，`docker search` 是快速查找镜像的方式，但对于更详细的信息，Docker Hub 网站通常更有用。
