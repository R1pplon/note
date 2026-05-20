# 使用 Docker 卷持久化数据

在这一步中，你将学习如何使用 Docker 卷来持久化数据。

## 目标

本步骤的目标是创建一个使用卷来持久化数据的 Docker 容器。

## 结果示例

1. 在 `/home/labex/project` 路径下创建一个名为 `docker-volume` 的新目录，并进入该目录。

```
labex:docker-volume/ $ pwd
/home/labex/project/docker-volume
```

1. 在 `/home/labex/project/docker-volume` 路径下创建一个名为 `Dockerfile` 的文件，内容如下：

   - 使用 `python:3` 作为父镜像
   - 将工作目录设置为 `/app`
   - 将当前目录的内容添加到容器的 `/app` 路径下
   - 创建一个挂载点 `/data`
   - 运行 `["python", "app.py"]` 命令来启动服务器

2. 在你的项目目录 `/home/labex/project/docker-volum` 下创建一个名为 `app.py` 的文件，内容如下：

```python
print("Hello, Docker-volume!")
```

4. 使用 `docker build` 命令构建名为 `python-volume` 的镜像。

```
labex:docker-volume/ $ docker images | grep python-v
python-volume                 latest    e2c7341705ce   27 seconds ago   1.02GB
```

5. 使用 `python-volume` 镜像运行 Docker 容器，使用 `-v` 挂载到宿主机的 `$(pwd)/data` 路径。使用 `-it` 与容器进行交互。在容器内部，向挂载的卷写入数据。最后，使用 `CTRL+p+q` 使容器保持运行状态。

```
labex:Desktop/ $ docker ps | grep vol
660bd38a649c   python-volume   "bash"    43 seconds ago   Up 41 seconds             thirsty_kirch
```

6. 验证 Docker 卷是否持久化了数据。在宿主机的当前目录下，应该有一个名为 `data` 的目录和一个名为 `hello.txt` 的文件。

```
labex:docker-volume/ $ cat data/hello.txt
Hello, World!
```
