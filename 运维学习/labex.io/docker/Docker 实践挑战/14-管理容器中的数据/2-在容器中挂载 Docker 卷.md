# 在容器中挂载 Docker 卷

现在我们已经有了一个卷，让我们把它挂载到容器上。这一步将演示如何让运行中的容器使用持久化存储。

## 任务

1. 基于 `nginx` 镜像运行一个名为 `my-container` 的新 Docker 容器。
2. 将 `myvolume` 卷挂载到容器内的 `/app/data` 路径。
3. 使用 `docker inspect` 命令验证卷是否已正确挂载。

## 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 以分离模式（detached mode）运行容器。
- 启动容器时使用 `-v` 选项来挂载卷。

## 示例

运行容器后，`docker inspect my-container` 的输出应包含：

```json
"Mounts": [
    {
        "Type": "volume",
        "Name": "myvolume",
        "Source": "/var/lib/docker/volumes/myvolume/_data",
        "Destination": "/app/data",
        "Driver": "local",
        "Mode": "z",
        "RW": true,
        "Propagation": ""
    }
]
```
