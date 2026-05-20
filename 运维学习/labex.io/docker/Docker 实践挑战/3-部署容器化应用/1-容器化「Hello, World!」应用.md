# 容器化「Hello, World!」应用

在这一步中，你将使用 Docker 容器化一个简单的「Hello, World!」应用。

## 目标

本步骤的目标是创建一个运行「Hello, World!」应用的 Docker 容器。

## 结果示例

1. 在 `/home/labex/project` 路径下创建一个名为 `docker-hello-world` 的新目录，并进入该目录。

```
labex:docker-hello-world/ $ pwd
/home/labex/project/docker-hello-world
```

2. 在 `/home/labex/project/docker-hello-world` 路径下创建一个名为 `Dockerfile` 的文件，内容如下：

   - 使用 `busybox` 作为父镜像
   - 运行 `echo "Hello, World!"` 命令

3. 使用 `docker build` 命令构建名为 `hello-world` 的镜像。

```
labex:docker-hello-world/ $ docker images | grep hell
hello-world                   latest    9d9fde0edf5c   15 seconds ago   4.26MB
```

4. 当你使用 `hello-world` 镜像运行 Docker 容器时，你应该能看到消息 "Hello, World!"。

```
labex:docker-hello-world/ $ docker run hello-world
Hello, World!
```
