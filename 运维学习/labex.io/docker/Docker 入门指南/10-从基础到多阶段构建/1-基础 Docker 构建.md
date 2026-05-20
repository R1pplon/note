# 基础 Docker 构建

我们将从基础开始。在这一步中，你将创建一个简单的 Docker 镜像，输出「Hello, World!」消息。这将让你熟悉 Dockerfile 的基本结构和 `docker build` 命令。

## 任务

1. 进入 `/home/labex/project` 下的 `hello-world` 目录。
2. 打开该目录中的 `Dockerfile` 并添加必要的指令。
3. 使用 `docker build` 命令构建一个名为 `hello-world` 的 Docker 镜像。

## 要求

- 在 `/home/labex/project/hello-world` 目录中执行所有操作。
- 在 Dockerfile 中使用 Alpine Linux 作为基础镜像。
- 运行该 Docker 镜像时应打印出「Hello, World!」。

## 示例

完成此步骤后，你应该能够运行以下命令并看到输出：

```
$ docker run hello-world
Hello, World!
```

太棒了，你成功创建了第一个 Docker 镜像！这个简单的例子展示了 Docker 镜像创建的基本原理。在下一步中，我们将在此基础上学习如何创建包含依赖项的更复杂的镜像。
