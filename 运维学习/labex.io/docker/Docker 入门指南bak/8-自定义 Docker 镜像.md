## 介绍
Docker 是一款强大的工具，用于将应用程序打包并分发为容器。这一过程的核心是 Docker 镜像，它们是预先构建好的软件包，包含了运行应用程序所需的一切，包括代码、依赖项和配置。在本实验中，你将学习如何通过集成额外的软件、库或配置来创建自定义 Docker 镜像，从而增强你的应用程序。

**在整个实验过程中，我们将使用 WebIDE（VS Code）来编辑文件。WebIDE 为文件编辑提供了一个熟悉且用户友好的界面，使处理配置文件和代码变得更加轻松。**
## 设置项目环境
让我们从创建一个项目目录并进入该目录开始。

**在整个实验过程中，我们将使用 WebIDE（VS Code）来编辑文件。WebIDE 为文件编辑提供了一个熟悉且用户友好的界面，使处理配置文件和代码变得更加轻松。**

1. 在 WebIDE 中打开终端。你可以通过点击顶部菜单中的「Terminal」，然后选择「New Terminal」来完成此操作。
2. 在终端中运行以下命令：

```bash
mkdir -p ~/project/docker
cd ~/project/docker
```

![显示项目目录创建命令的终端](https://labex.io/cdn-cgi/image/format=auto,quality=60,onerror=redirect/https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/docker/lab-custom-docker-images/assets/screenshot-20250716-RAAFeXYV@2x.png)

这将在 `~/project` 文件夹中创建一个名为 `docker` 的新目录，并将当前工作目录切换到该目录。

3. 验证你是否处于正确的目录中：

```bash
pwd
```

你应该在输出中看到 `/home/labex/project/docker`。

理解这些命令：

- `mkdir -p`：此命令用于创建目录。`-p` 标志允许它在父目录不存在时一并创建。
- `cd`：此命令用于切换当前目录。
- `pwd`：此命令用于打印当前工作目录。
## 创建一个简单的 Docker 镜像
现在，让我们创建一个运行 Nginx Web 服务器的简单 Docker 镜像。

1. 在 WebIDE 中，导航到文件资源管理器（通常是左侧边栏的第一个图标）。
2. 在文件资源管理器面板中右键点击并选择「New File」。将其命名为 `Dockerfile`（首字母 `D` 大写，且没有文件扩展名）。
3. 点击 `Dockerfile` 将其打开。添加以下内容：

```Dockerfile
FROM nginx
COPY index.html /usr/share/nginx/html/
```

这个 Dockerfile 定义了一个基于官方 Nginx 镜像的新镜像，并将一个名为 `index.html` 的文件复制到 Nginx 的默认文档根目录。

理解 Dockerfile：

- `FROM nginx`：这一行指定了我们要构建的基础镜像。在本例中，我们使用的是官方的 Nginx 镜像。
- `COPY index.html /usr/share/nginx/html/`：这一行将我们的 `index.html` 文件复制到容器内的 Web 根目录中。

4. 在同一目录下创建一个名为 `index.html` 的新文件。你可以再次在文件资源管理器面板中右键点击并选择「New File」。
5. 打开 `index.html` 并添加以下内容：

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Hello Docker!</title>
  </head>
  <body>
    <h1>Hello Docker!</h1>
    <p>This is a custom Docker image.</p>
  </body>
</html>
```

这是一个简单的 HTML 页面，将由我们的 Nginx 服务器提供服务。

6. 在 WebIDE 中打开终端（如果关闭了之前的终端，请点击 Terminal -> New Terminal），并使用以下命令构建 Docker 镜像：

```bash
docker build -t my-nginx .
```

此命令会构建一个标签为 `my-nginx` 的新 Docker 镜像。

理解该命令：

- `docker build`：这是构建 Docker 镜像的命令。
- `-t my-nginx`：`-t` 标志为我们的镜像打上 `my-nginx` 的标签名。
- `.`：这指定了构建上下文（包含 Dockerfile 的目录）为当前目录。

7. 构建完成后，验证镜像是否创建成功：

```bash
docker images
```

你应该能在输出列表中看到 `my-nginx` 镜像。
## 运行并测试自定义镜像
让我们基于新镜像运行一个容器并进行测试。

1. 在 WebIDE 终端中，使用以下命令启动容器：

```bash
docker run -d -p 8080:80 --name my-nginx-container my-nginx
```

理解该命令：

- `docker run`：此命令用于创建并启动一个新容器。
- `-d`：此标志使容器在分离模式（后台）下运行。
- `-p 8080:80`：这将宿主机的 8080 端口映射到容器的 80 端口。
- `--name my-nginx-container`：这为我们的容器分配了一个名称。
- `my-nginx`：这是我们用于创建容器的镜像名称。

2. 验证容器是否正在运行：

```bash
docker ps
```

你应该在输出中看到 `my-nginx-container`。此命令显示所有正在运行的容器。

3. 要查看网页内容，请使用 `curl` 命令：

```bash
curl http://localhost:8080
```

你应该会在终端中看到 `index.html` 文件的 HTML 内容。

如果你好奇 `curl` 的作用，它是一个用于从服务器传输数据或向服务器传输数据的工具。在本例中，我们使用它从 Web 服务器获取内容。

你也可以导航到 LabEx 虚拟机的顶部，点击「+」创建一个新的 Web 服务，然后输入端口 `8080` 来查看内容。

![Web 服务器端口设置](https://labex.io/cdn-cgi/image/format=auto,quality=60,onerror=redirect/https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/docker/lab-custom-docker-images/assets/20240821-13-51-05-sijgxPuf.png)
## 向镜像添加自定义软件
现在，让我们修改 Docker 镜像以包含额外的软件。我们将以添加 `curl` 工具为例。

1. 在 WebIDE 中打开 `Dockerfile`。你可以通过在文件资源管理器面板中点击 `Dockerfile` 来实现。
2. 将 `Dockerfile` 的内容修改为：

```Dockerfile
FROM nginx
RUN apt-get update && apt-get install -y curl
COPY index.html /usr/share/nginx/html/
```

这个 Dockerfile 添加了一个新的 `RUN` 指令，该指令使用 `apt-get` 包管理器更新包索引并安装 `curl` 工具。

理解新添加的行：

- `RUN apt-get update && apt-get install -y curl`：这一行先更新软件包列表（`apt-get update`），然后安装 curl（`apt-get install -y curl`）。`-y` 标志会在安装过程中自动对所有提示回答「是」。

3. 在 WebIDE 中按 `Ctrl+S`（Mac 上为 `Cmd+S`）保存文件。
4. 在 WebIDE 终端中，使用新标签重新构建 Docker 镜像：

```bash
docker build -t my-nginx-curl .
```

此命令将构建一个名为 `my-nginx-curl` 的新 Docker 镜像，其中包含了 `curl` 工具。

5. 验证新镜像是否已创建：

```bash
docker images
```

你应该能在输出列表中看到 `my-nginx-curl` 镜像。
## 使用 Curl 测试自定义镜像
让我们基于新镜像运行一个容器并测试 `curl` 工具。

1. 在 WebIDE 终端中，使用以下命令启动一个新容器：

```bash
docker run -d --name curl-container my-nginx-curl
```

此命令启动一个基于 `my-nginx-curl` 镜像的新容器，并将其命名为 `curl-container`。

2. 在运行中的容器内执行 bash shell：

```bash
docker exec -it curl-container bash
```

此命令在运行中的容器内部打开一个交互式的 bash shell。

理解该命令：

- `docker exec`：在运行中的容器内运行命令。
- `-it`：这些标志分配一个伪终端并保持标准输入（STDIN）打开，允许你与 shell 进行交互。
- `curl-container`：这是我们容器的名称。
- `bash`：这是我们在容器中运行的命令（打开 bash shell）。

3. 你现在应该看到一个新的提示符，表明你已进入容器内部。在容器内测试 `curl` 工具：

```bash
curl http://localhost
```

你应该会在终端中看到 `index.html` 文件的 HTML 内容。

4. 退出容器的 bash shell：

```bash
exit
```

此命令将带你回到宿主系统的 shell。
## 在镜像中使用环境变量
在这一步中，我们将修改 Docker 镜像，使用环境变量进行自定义配置。

1. 在 WebIDE 中再次打开 `Dockerfile`。
    
2. 将 `Dockerfile` 的内容修改为：
    

```Dockerfile
FROM nginx
ENV NGINX_PORT 9000
RUN sed -i "s/listen[[:space:]]*80;/listen $NGINX_PORT;/g" /etc/nginx/conf.d/default.conf
COPY index.html /usr/share/nginx/html/
```

这个 Dockerfile 添加了一个 `ENV` 指令，将 `NGINX_PORT` 变量设置为 `9000`。它还包含了一个 `RUN` 指令，通过修改 Nginx 配置来使用此端口。

理解新添加的行：

- `ENV NGINX_PORT 9000`：这设置了一个值为 `9000` 的环境变量 `NGINX_PORT`。
- `RUN sed -i "s/listen[[:space:]]*80;/listen $NGINX_PORT;/g" /etc/nginx/conf.d/default.conf`：这使用 `sed` 命令在 Nginx 配置文件中将默认端口（80）替换为我们的环境变量。

3. 在 WebIDE 中保存文件。
    
4. 在 WebIDE 终端中，使用新标签重新构建 Docker 镜像：
    

```bash
docker build -t my-nginx-env .
```

5. 基于新镜像运行容器：

```bash
docker run -d -p 9000:9000 --name env-container my-nginx-env
```

此命令启动一个基于 `my-nginx-env` 镜像的新容器，并将宿主机的 9000 端口映射到容器的 9000 端口。请注意，环境变量已经在 Dockerfile 中设置好了，因此不需要在 `docker run` 命令中再次设置。

6. 验证 Web 服务器是否在指定端口上运行：

```bash
curl http://localhost:9000
```

你应该会在终端中看到 `index.html` 文件的 HTML 内容。
## 在 Dockerfile 中使用 ENTRYPOINT
在这一步中，我们将学习如何在 Dockerfile 中使用 `ENTRYPOINT` 指令，并使用一个不同的端口（9100）。

1. 在 WebIDE 中再次打开 `Dockerfile`。
    
2. 将 `Dockerfile` 的内容修改为：
    

```Dockerfile
FROM nginx
COPY index.html /usr/share/nginx/html/
COPY start.sh /start.sh
RUN chmod +x /start.sh
ENTRYPOINT ["/start.sh"]
```

这个 Dockerfile 移除了环境变量和 `sed` 命令。取而代之的是，我们将使用 `ENTRYPOINT` 脚本在运行时处理配置。这使得我们的镜像更加灵活。

3. 在同一目录下创建一个名为 `start.sh` 的新文件，内容如下：

```bash
#!/bin/bash
# 如果未设置 NGINX_PORT，则设置默认端口
export NGINX_PORT=${NGINX_PORT:-9100}
# 替换 nginx 配置中的端口
sed -i "s/listen[[:space:]]*80;/listen $NGINX_PORT;/g" /etc/nginx/conf.d/default.conf
echo "Starting Nginx on port $NGINX_PORT"
nginx -g 'daemon off;'
```

此脚本设置了一个默认端口，在容器启动时修改 Nginx 配置，打印一条显示 Nginx 运行端口的消息，然后启动 Nginx。

4. 在 WebIDE 中保存这两个文件。
    
5. 在 WebIDE 终端中，使用新标签重新构建 Docker 镜像：
    

```bash
docker build -t my-nginx-entrypoint .
```

6. 基于新镜像运行容器。我们将通过环境变量将端口设置为 9100。

```bash
docker run -d -p 9100:9100 -e NGINX_PORT=9100 --name entrypoint-container my-nginx-entrypoint
```

7. 检查容器日志以查看启动消息：

```bash
docker logs entrypoint-container
```

你应该在输出中看到消息「Starting Nginx on port 9100」。

8. 验证 Web 服务器是否在新端口上正常运行：

```bash
curl http://localhost:9100
```

你应该会在终端中看到 `index.html` 文件的 HTML 内容。
## 总结
在本实验中，你学习了如何创建能为应用程序增值的自定义 Docker 镜像。你从创建一个带有 Web 服务器的简单镜像开始，逐步进阶到添加自定义软件以及使用环境变量进行配置。在整个实验中，你使用了 WebIDE（VS Code）来编辑文件，使过程更加直观且用户友好。

以下是你所完成工作的回顾：

1. 设置了项目环境并学习了基础终端命令。
2. 创建了一个带有 Nginx 和自定义 HTML 页面的简单 Docker 镜像。
3. 构建并运行了 Docker 容器，学习了端口映射和容器命名。
4. 修改了 Docker 镜像以包含额外的软件（curl）。
5. 使用 `docker exec` 在容器内部运行命令。
6. 将环境变量集成到 Docker 镜像中，以便更轻松地进行配置。