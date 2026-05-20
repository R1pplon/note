# 构建多阶段 Docker 镜像

在最后的挑战中，我们将深入研究多阶段构建（Multi-stage builds）。这种高级技术允许你通过在构建过程中使用多个阶段来创建更高效的 Docker 镜像。你将为一个 Go 应用程序创建一个 Docker 镜像，使用一个阶段来编译代码，另一个阶段来创建一个精简的、生产就绪的镜像。

## 任务

1. 进入 `/home/labex/project` 下的 `go-app` 目录。
2. 打开该目录中的 `Dockerfile` 并添加多阶段构建的指令。
3. 检查 `main.go` 文件以了解这个简单的 Go 应用程序。
4. 使用 `docker build` 命令构建一个名为 `go-app` 的 Docker 镜像。

## 要求

- 在 `/home/labex/project/go-app` 目录中执行所有操作。
- 使用 `golang:1.14-alpine` 作为构建阶段的基础镜像，使用 `alpine` 作为最终阶段的基础镜像。
- 运行该 Go 应用程序时应打印出「Hello, World!」。
- 应使用 `go build` 命令编译 Go 应用程序。
- 最终的 Docker 镜像应仅包含编译后的二进制文件，而不包含 Go 开发环境。

## 示例

完成此步骤后，你应该能够运行以下命令并看到输出：

```
$ docker run go-app
Hello, World!
```
