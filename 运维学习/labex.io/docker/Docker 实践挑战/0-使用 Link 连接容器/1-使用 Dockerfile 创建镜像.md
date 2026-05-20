# 使用 Dockerfile 创建镜像

在连接容器之前，我们需要至少有一个容器化的应用程序。在这一步中，你将为 `my-app` 镜像创建一个 Dockerfile。该 Dockerfile 将定义容器内运行的环境和应用程序。

## 任务

本步骤的目标是为 `my-app` 镜像创建一个 Dockerfile。

## 要求

- 机器上必须已安装 Docker。
- 你应该有一个想要容器化的现有 `my-app` 应用程序。

## 示例结果

1. 为挑战创建必要的文件：

   在 `/home/labex/project/` 路径下创建一个名为 `Dockerfile` 的新文件，内容如下：

   - 使用 `python:3.7-slim` 作为基础镜像。
   - 将工作目录设置为 `/app`。
   - 将当前目录的内容复制到容器内的 `/app` 目录。
   - 安装所需的软件包。
   - 向外部暴露 `80` 端口。
   - 定义一个环境变量（尽管在此示例中未使用，但请保留该指令）。
   - 容器启动时运行 `app.py`。

   在项目目录 `/home/labex/project/` 下创建一个名为 `app.py` 的文件，内容如下：

   ```python
   import os

   os.system("wssh --address='0.0.0.0' --port=80")
   ```

   在项目目录 `/home/labex/project/` 下创建一个名为 `requirements.txt` 的文件，内容如下：

   ```bash
   webssh==1.6.2
   ```

   ```bash
   labex:project/ $ pwd
   /home/labex/project
   labex:project/ $ ll
   total 12K
   -rw-r--r-- 1 labex labex 59 Jan 24 15:21 app.py
   -rw-r--r-- 1 labex labex 163 Jan 24 15:19 Dockerfile
   -rw-r--r-- 1 labex labex 14 Jan 24 15:21 requirements.txt
   ```

2. 使用 `docker build` 命令构建 `my-app` 镜像。

   ```bash
   labex:project/ $ docker images | grep my-app
   my-app latest 266edf714faf 30 seconds ago 170MB
   ```

3. 使用 `my-app` 镜像启动一个新容器，并注意端口映射。

   ```bash
   labex:project/ $ docker ps
   CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
   11f06f08d2d3 my-app "python app.py" 4 seconds ago Up 4 seconds 0.0.0.0:8080- hopeful_elgamal > 80/tcp
   ```

4. 打开 Web 浏览器并访问 `http://localhost:8080`，验证应用程序是否运行正常。

   ![Web app running in browser](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-connect-containers-with-link/zh/../assets/20240302-10-47-19-OHrX5mr8.png)
