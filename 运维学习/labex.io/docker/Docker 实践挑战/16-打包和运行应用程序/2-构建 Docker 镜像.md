# 构建 Docker 镜像

在这一步中，你将学习如何从 Dockerfile 构建 Docker 镜像。Dockerfile 是一个文本文件，其中包含构建 Docker 镜像的指令，例如指定基础镜像、复制文件、设置环境变量和运行命令。

## 目标

本步骤的目标是构建一个自定义 Web 应用程序的 Docker 镜像。

## 要求

- 一个文本编辑器用于创建 Dockerfile。
- 一个简单 Web 应用程序的源代码。

## 结果示例

1. 创建一个名为 `my-app` 的新目录，并在 `/home/labex/project` 路径下进入该目录。

```
labex:my-app/ $ pwd
/home/labex/project/my-app
```

2. 在该目录中创建一个名为 `Dockerfile` 的文件，内容如下：

   - 使用 `python:3.7-slim` 作为父镜像
   - 将工作目录设置为 `/app`
   - 将当前目录的内容复制到容器内的 `/app`
   - 使用 pip 安装所需包，并且不使用缓存目录来存放下载的包，使用 `-r` 标志指定 `requirements.txt` 文件
   - 设置环境变量 `PORT=80`
   - 暴露端口
   - 运行 `["python", "app.py"]` 命令启动服务器

3. 在该目录中创建一个名为 `requirements.txt` 的文件，内容如下：

```bash
flask
```

4. 在该目录中创建一个名为 `app.py` 的文件，内容如下：

```python
from flask import Flask
import os

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello, World!"

if __name__ == "__main__":
    app.run(debug=False, host='0.0.0.0', port=int(os.environ.get('PORT', 80)))
```

5. 使用 `docker build` 命令构建 `my-app` 镜像。

```
labex:my-app/ $ docker images | grep my-app
my-app                        latest     e310a0cb9319   About a minute ago   136MB
```

6. 使用 `docker run` 命令使用 `my-app` 镜像运行一个 Docker 容器。

在另一个终端中运行以下命令以验证容器是否正在运行：

```
labex:~/ $ docker ps | grep my-app
a36a73816cfc   my-app    "python app.py"   About a minute ago   Up About a minute   0.0.0.0:8888->80/tcp, :::8888->80/tcp   upbeat_edison
```

7. 在你的 Web 浏览器中打开 `http://localhost:8888` 访问 Web 应用程序。

![Docker 容器运行 Web 应用](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-package-and-run-applications/zh/../assets/challenge-package-and-run-applications-2-7.png)
