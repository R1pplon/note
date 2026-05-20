# 从 Docker 卷读取数据

既然我们已经向卷中写入了数据，现在来验证是否可以将其读取出来，以此证明数据在容器操作中的持久性。

## 任务

1. 使用 `docker exec` 命令进入 `my-container` 容器。
2. 读取并显示 `/app/data/hello.txt` 文件的内容。

## 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 使用 `cat` 命令显示文件内容。

## 示例

你的命令输出应该是：

```
Hello, World!
```
