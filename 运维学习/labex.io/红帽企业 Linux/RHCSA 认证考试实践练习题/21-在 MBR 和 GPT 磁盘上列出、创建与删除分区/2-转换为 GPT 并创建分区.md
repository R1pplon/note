# 转换为 GPT 并创建分区

现在，你将把同一块磁盘 `/dev/vdb` 转换为现代的 GPT 分区方案，并使用 `gdisk` 工具执行分区任务。`gdisk` 会自动检测旧的 MBR 格式并提供转换选项，同时保留你在上一步中创建的分区。

## 要求

- 使用 `gdisk` 命令管理 `/dev/vdb` 磁盘上的分区。
- 当系统提示时，允许 `gdisk` 将 MBR 磁盘转换为 GPT。
- 创建一个大小为 1G 的新分区。
- 创建完成后，验证新分区。

## 示例

将磁盘转换为 GPT 后，原始分区会被保留。当你创建一个新的 1G 分区时，磁盘将拥有两个分区。`sudo gdisk -l /dev/vdb` 的输出应该类似于这样：

```
Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         1026047   500.0 MiB   8300  Linux filesystem
   2         1026048         3123199   1024.0 MiB  8300  Linux filesystem
```
