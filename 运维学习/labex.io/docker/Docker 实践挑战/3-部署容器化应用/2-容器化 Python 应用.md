# 容器化 Python 应用

在这一步中，你将使用 Docker 容器化一个 Python 应用。

## 目标

本步骤的目标是创建一个运行 Python 应用的 Docker 容器。

## 结果示例

1. 在 `/home/labex/project` 路径下创建一个名为 `docker-python` 的新目录，并进入该目录。

```
labex:docker-python/ $ pwd
/home/labex/project/docker-python
```

2. 在 `/home/labex/project/docker-python` 路径下创建一个名为 `Dockerfile` 的文件，内容如下：

   - 使用 `python:3` 作为父镜像
   - 将工作目录设置为 `/app`
   - 将当前目录的内容添加到容器的 `/app` 路径下
   - 运行 `["python", "app.py"]` 命令来启动服务器

3. 在你的项目目录 `/home/labex/project/docker-python` 下创建一个名为 `app.py` 的文件，内容如下：

```python
print("Hello, Docker!")
```

4. 使用 `docker build` 命令构建名为 `python-app` 的镜像。

```
labex:docker-python/ $ docker images | grep python-app
python-app                    latest    df9f5dea8f65   20 seconds ago   1.02GB
```

5. 当你使用 `python-app` 镜像运行 Docker 容器时，你应该能看到 Python 应用的输出。

```
labex:docker-python/ $ docker run python-app
Hello, Docker!
```
