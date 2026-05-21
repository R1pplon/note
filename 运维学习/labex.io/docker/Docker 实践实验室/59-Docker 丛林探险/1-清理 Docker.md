# 清理 Docker

## 任务

- 使用 `prune` 命令移除所有已停止的容器。
- 从 Docker 中移除所有未使用的网络。

## 要求

你需要在指定的当前工作目录下的 zsh 终端中执行以下命令：

- 默认的工作目录是 `/home/labex/project`。
- 使用 `docker prune` 命令来完成这些任务。

## 示例

```
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
cd36e23e58442aa58cf27f2d020573c5ea251fe97ff00c76eed3a30a5a4c7bc0
...

WARNING! This will remove all custom networks not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Networks:
mynetwork
minikube
```
