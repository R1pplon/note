## 介绍

在本实验中，我们将专门研究 `docker run` 命令及其各种参数。`docker run` 是 Docker 操作的基础，它允许我们根据特定配置创建并启动容器。

通过掌握该命令的参数，你将能够更精准地控制容器化应用，从而提升高效部署和管理 Docker 容器的能力。

我们将涵盖广泛的参数，包括容器命名、后台运行模式、端口映射、卷挂载、环境变量、资源限制等。

其中一些参数你可能已经有所了解，而另一些对你来说可能是全新的。

## 基础运行与容器命名

让我们从 `docker run` 的基础知识开始，探索如何为容器命名。

首先，运行一个基础的 Nginx 容器：

```bash
docker run nginx
```

此命令在前台运行 Nginx 容器。你会在终端看到一连串的日志输出。这是因为容器在前台运行，直接向你展示日志。

要停止此容器，请按 Ctrl+C。你可能会注意到它需要几秒钟才能停止 —— 这是正常现象，因为 Docker 正在给容器时间进行优雅停机。

现在，让我们以分离模式（后台）运行它并为其指定一个名称：

```bash
docker run -d --name my-nginx nginx
```

让我们分解一下这个命令：

- `docker run`：运行容器的基础命令。
- `-d`：此选项以分离模式（detached mode）运行容器，意味着它在后台运行。你不会在终端看到任何输出。
- `--name my-nginx`：这为容器分配了名称「my-nginx」。如果你不指定名称，Docker 会分配一个随机名称。
- `nginx`：这是我们用于创建容器的镜像名称。

运行此命令后，你会看到一长串字符。这就是容器 ID。Docker 已经在后台启动了容器。

如果你遇到提示名称已被占用的错误，说明你已经有一个同名容器了。你可以选择一个不同的名称，或者删除现有容器（我们将在后续实验中学习如何操作）。

## 端口映射

`docker run` 中的 `-p` 参数允许我们将容器的端口映射到宿主机。这对于从宿主机访问容器内运行的服务至关重要。

运行一个带有端口映射的 Nginx 容器：

```bash
docker run -d --name nginx-mapped -p 8080:80 nginx
```

让我们分解该命令的新部分：

- `-p 8080:80`：这将宿主机的 8080 端口映射到容器的 80 端口。格式始终是 `host_port:container_port`。

默认情况下，Nginx 在容器内部的 80 端口上运行。通过将其映射到宿主机的 8080 端口，我们可以通过在浏览器中访问 localhost:8080 来访问它。

现在，让我们验证 Nginx 欢迎页面是否可以访问。我们将使用 `curl` 命令，它允许我们从命令行发起 HTTP 请求：

```bash
curl http://localhost:8080
```

你应该能看到 Nginx 欢迎页面的 HTML 内容。如果你没有安装 `curl`，可以通过以下命令安装：

```bash
sudo apt-get update && sudo apt-get install -y curl
```

如果你仍然无法访问页面，请检查以下几点：

1. 确保容器正在运行：`docker ps | grep nginx-mapped`
2. 检查端口是否已成功映射：`docker port nginx-mapped`
3. 如果你使用的是云服务器，请确保防火墙允许 8080 端口的流量。

## 卷挂载

`docker run` 中的 `-v` 参数允许我们挂载卷（Volumes），在宿主机和容器之间共享数据。这对于持久化数据或为容器提供配置文件非常有用。

首先，在宿主机上创建一个简单的目录结构：

```bash
mkdir -p ~/project/nginx-data
echo "<html><body><h1>Hello from mounted volume</h1></body></html>" > ~/project/nginx-data/index.html
```

这些命令的作用如下：

1. 在家目录的 `project` 文件夹内创建一个新目录 `nginx-data`。
2. 在这个新目录中创建一个名为 `index.html` 的简单 HTML 文件。

现在，让我们运行一个 Nginx 容器并挂载这个目录：

```bash
docker run -d --name nginx-volume -p 8081:80 -v ~/project/nginx-data:/usr/share/nginx/html nginx
```

让我们分解这个命令：

- `docker run`：运行新容器的命令。
- `-d`：以分离模式（后台）运行容器。
- `--name nginx-volume`：为容器分配名称「nginx-volume」。
- `-p 8081:80`：将宿主机的 8081 端口映射到容器的 80 端口。
- `-v ~/project/nginx-data:/usr/share/nginx/html`：将宿主机上的 `nginx-data` 目录挂载到容器内的 `/usr/share/nginx/html` 目录。这是 Nginx 查找服务内容的地方。
- `nginx`：用于创建容器的镜像名称。

现在，验证是否正在提供自定义页面：

```bash
curl http://localhost:8081
```

你应该看到自定义 HTML 文件的内容：「Hello from mounted volume!」

如果你没看到自定义内容，请检查：

1. 确保宿主机系统上存在 `~/project/nginx-data/index.html` 文件。
2. 检查容器是否正在运行：`docker ps | grep nginx-volume`
3. 检查 Nginx 日志是否有错误：`docker logs nginx-volume`

这种将宿主机目录挂载到容器的方法称为绑定挂载（bind mount）。这是在宿主机和容器之间共享文件的直接方式。请记住以下要点：

1. 宿主机目录路径必须是绝对路径。
2. 如果宿主机目录不存在，Docker 会自动创建它。
3. 对该目录中文件所做的任何更改（无论是在宿主机还是容器中）都会立即对双方可见。
4. 注意权限问题：容器默认以 root 用户运行，这可能会创建宿主机用户无法修改的文件。

通过使用这种方法，我们避免了「不是目录」的错误，因为我们挂载的是目录而不是单个文件。这种方法让你在添加、删除或修改文件时更具灵活性，无需重新创建容器。

## 环境变量

`docker run` 中的 `-e` 参数允许我们在容器中设置环境变量。这对于在不更改代码的情况下配置容器内运行的应用非常有用。

运行一个带有环境变量的容器：

```bash
docker run -d --name nginx-env -e NGINX_HOST=mywebsite.com -e NGINX_PORT=80 nginx
```

分解新部分：

- `-e NGINX_HOST=mywebsite.com`：设置一个名为 `NGINX_HOST` 的环境变量，值为 `mywebsite.com`。
- `-e NGINX_PORT=80`：设置另一个环境变量 `NGINX_PORT`，值为 `80`。

环境变量是键值对，可以被容器内运行的进程访问。许多 Docker 镜像被设计为使用特定的环境变量进行配置。

你可以验证环境变量：

```bash
docker exec nginx-env env | grep NGINX_
```

该命令的作用如下：

- `docker exec nginx-env`：告诉 Docker 在运行中的 `nginx-env` 容器内执行命令。
- `env`：打印所有环境变量的命令。
- `| grep NGINX_`：过滤输出，仅显示包含「NGINX_」的行。

你应该能看到列出的两个环境变量。

如果你没看到环境变量，请检查：

1. 容器是否在运行？通过 `docker ps | grep nginx-env` 验证。
2. 你在 `docker run` 命令中拼写的环境变量名称是否正确？

## 资源限制
Docker 允许你使用 `docker run` 中的各种参数对容器设置资源限制。这对于管理宿主机系统的性能和稳定性至关重要，尤其是在运行多个容器时。

运行一个带有内存和 CPU 限制的容器：

```bash
docker run -d --name nginx-limited --memory 256m --cpus 0.5 nginx
```

分解新部分：

- `--memory 256m`：将容器限制为 256 MB 内存。「m」代表兆字节。你也可以使用「g」代表吉字节。
- `--cpus 0.5`：限制容器最多使用半个 CPU 核心。

这些限制可以防止容器消耗超过指定的资源，从而避免单个容器垄断宿主机的资源。

你可以验证这些限制是否已正确应用：

```bash
docker inspect -f '{{.HostConfig.Memory}}' nginx-limited
docker inspect -f '{{.HostConfig.NanoCpus}}' nginx-limited
```

第一个命令应输出 `268435456`（以字节为单位的 256MB），第二个命令应输出 `500000000`（以纳单位表示的 0.5 CPU）。

如果你看到不同的值，请仔细检查你的 `docker run` 命令，确保正确指定了限制。

注意：资源限制设置得过低可能会导致容器性能不佳甚至崩溃。如果你的容器出现问题，请尝试增加这些限制。
## 网络设置
`docker run` 中的 `--network` 参数允许你将容器连接到特定网络。这对于容器间通信以及隔离容器组非常有用。

首先，创建一个自定义桥接网络：

```bash
docker network create my-custom-network
```

这将创建一个名为 `my-custom-network` 的新桥接网络。桥接网络是 Docker 中最常见的网络类型。

现在，运行一个连接到该网络的容器：

```bash
docker run -d --name nginx-networked --network my-custom-network nginx
```

`--network my-custom-network` 选项将容器连接到我们刚刚创建的网络。

同一网络上的容器可以使用它们的容器名称作为主机名相互通信。这使得将各种服务链接在一起变得非常容易。

如果你收到网络不存在的错误，请确保你已使用 `docker network create` 命令正确创建了网络。
## 重启策略
`docker run` 中的 `--restart` 参数允许你为容器指定重启策略。这对于确保容器在崩溃或 Docker 守护进程重启后仍能保持运行非常有用。

运行一个带有重启策略的容器：

```bash
docker run -d --name nginx-restart --restart unless-stopped nginx
```

`--restart unless-stopped` 选项将重启策略设置为「除非停止」，这意味着除非用户显式停止容器，否则容器将自动重启。

其他重启策略包括：

- `no`：默认值。不自动重启容器。
- `on-failure`：仅当容器以非零状态退出时才重启。
- `always`：无论退出状态如何，始终重启容器。

你可以验证重启策略：

```bash
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' nginx-restart
```

输出应为 `unless-stopped`。

如果你没看到预期的输出，请检查你的 `docker run` 命令以确保正确指定了重启策略。
## 工作目录与命令
在这一步中，我们将探索如何在容器内设置工作目录，以及在容器启动时运行自定义命令。

`docker run` 中的 `-w` 参数用于设置容器内部的工作目录，你可以在镜像名称之后指定要运行的命令。

让我们结合这些概念：

```bash
docker run -d --name nginx-custom -w /app nginx sh -c "mkdir -p /app && touch newfile.txt && nginx -g 'daemon off;'"
```

让我们分解这个命令：

- `-d`：以分离模式（后台）运行容器。
- `--name nginx-custom`：将容器命名为「nginx-custom」。
- `-w /app`：将容器内部的工作目录设置为 `/app`。
- `nginx`：要使用的镜像名称。
- `sh -c "..."`：运行 shell 命令。
    - `mkdir -p /app`：如果 `/app` 目录不存在则创建它。
    - `&&`：如果前一个命令成功，则运行下一个命令。
    - `touch newfile.txt`：创建一个名为 `newfile.txt` 的空文件。
    - `&&`：如果前一个命令成功，则运行下一个命令。
    - `nginx -g 'daemon off;'`：在前台启动 Nginx，保持容器运行。

现在，让我们验证容器是否正在运行以及文件是否已创建：

```bash
docker ps | grep nginx-custom
docker exec nginx-custom ls -l /app/newfile.txt
```

第一个命令应显示容器正在运行，第二个命令应列出容器内 `/app` 目录中 `newfile.txt` 文件的详细信息。
## 总结
在本实验中，我们深入研究了 `docker run` 命令，探索了它的各种参数和选项。我们涵盖了：

1. 基础容器运行与命名
2. 端口映射，以便从宿主机访问容器服务
3. 卷挂载，在宿主机和容器之间共享数据
4. 设置环境变量进行容器配置
5. 应用资源限制以管控容器资源消耗
6. 网络设置以实现容器间通信
7. 重启策略以提高容器可靠性
8. 工作目录与命令指定，用于容器启动定制

`docker run` 的这些参数为配置和管理 Docker 容器提供了强大的工具。通过掌握这些选项，你可以创建更复杂、更符合特定需求的容器部署。你可以控制容器如何与宿主机系统交互、可以消耗哪些资源，以及它们在不同场景下的行为。