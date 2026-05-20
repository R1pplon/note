# 管理 Docker 容器

在这一步骤中，我们将学习如何管理 Docker 容器。

## 目标

你的目标是使用 `docker ps`、`docker stop` 和 `docker rm` 来管理正在运行的容器。

## 结果示例

以下是你在本步骤结束时应能完成的效果示例：

1. 列出所有正在运行的容器。
2. 停止名为 `my-web` 的容器。
3. 删除我们已经停止的 `my-web` 容器。

```
labex:hello-docker/ $ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
labex:hello-docker/ $
```

## 要求

- 你的机器上必须已安装 Docker。
