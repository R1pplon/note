# 构建 Docker 镜像

在这一步中，你将使用刚才编写的 `Dockerfile` 来创建镜像。

## 任务

- 构建一个名为 `myapp` 的新镜像。

## 示例

以下是你应当完成的操作示例：

1. 使用 `docker build` 命令构建名为 `myapp` 的新镜像。

    ```bash
    docker build -t myapp .
    ```

2. 使用 `docker images` 命令验证镜像是否构建成功。

## 要求

要完成此挑战，你需要：

- 在 `/home/labex/project/myapp` 目录下执行 `docker build` 命令。
- 将镜像命名为 `myapp`。
