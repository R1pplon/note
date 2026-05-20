# 备份与恢复 Docker 卷

在最后一步中，你将学习如何备份 Docker 卷中的数据并将其恢复到新卷中。这对于数据迁移和灾难恢复场景至关重要。

## 任务

1. 将 `myvolume` 卷中的数据备份为名为 `myvolume.tar.gz` 的压缩包，存放在 `/home/labex/project` 目录下。
2. 创建一个名为 `mynewvolume` 的新 Docker 卷。
3. 将备份数据恢复到 `mynewvolume` 中。

## 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 在备份和恢复操作中，使用带有 `--rm` 选项的临时容器运行 `docker run`。

## 示例

完成备份和恢复过程后，验证新卷的内容应显示：

```
$ docker run --rm -v mynewvolume:/app/data alpine cat /app/data/hello.txt
Hello, World!
```
