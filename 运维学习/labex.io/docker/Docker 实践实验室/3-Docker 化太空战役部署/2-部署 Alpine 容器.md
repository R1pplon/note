# 部署 Alpine 容器

在这一步中，你将配置一个 Alpine 容器，以确保舰队内部数据的安全传输。

## 任务

- 从 Docker Hub 拉取 Alpine Docker 镜像。
- 从 Alpine 镜像运行一个 Docker 容器。
- 验证 Alpine 容器部署成功。

## 要求

- 使用 Docker Hub 上可用的 Alpine Docker 镜像。
- 所有操作都通过命令行执行。

## 示例

完成此步骤后，Alpine 容器应该已经启动并运行，其轻量级而强大的功能已准备好支持舰队的数据处理需求。你可以使用以下命令进行检查：

```bash
$ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
2289906cd29a alpine "/bin/sh" 12 seconds ago Up 11 seconds beautiful_shtern
```
