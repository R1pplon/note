# 检查 Docker 环境

在此步骤中，你将确认 Docker 是否可用，以及 Jenkins 环境是否运行在 Docker 之上。

在 LabEx 虚拟机中打开终端，检查 Docker 版本：

```bash
docker --version
```

你应该会看到类似以下的输出：

```plaintext
Docker version ...
```

接下来，列出正在运行的容器：

```bash
docker ps
```

查找名为 `jenkins` 的容器。这是整个实验中使用的 Jenkins 控制器。输出包含多个列，但重要的字段是镜像名称、端口映射和容器名称：

```plaintext
CONTAINER ID   IMAGE                    ...   PORTS                  NAMES
...            jenkins/jenkins:latest   ...   0.0.0.0:8080->8080/tcp jenkins
```

关键信息是容器名称、镜像和端口映射。在本实验中，你无需启动或删除任何容器。
