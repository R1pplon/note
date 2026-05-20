# 向 Docker 卷写入数据

在这一步中，你将学习如何向挂载的 Docker 卷写入数据。即使容器被删除，这些数据也会持久保存。

## 任务

1. 使用 `docker exec` 命令进入 `my-container` 容器。
2. 在容器内的 `/app/data` 目录下创建一个名为 `hello.txt` 的文件。
3. 将内容「Hello, World!」写入 `hello.txt` 文件。

## 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 使用 `echo` 命令将内容写入文件。

## 示例

执行命令后，你应该能够看到该文件：

```
$ docker exec my-container ls /app/data
hello.txt
```
