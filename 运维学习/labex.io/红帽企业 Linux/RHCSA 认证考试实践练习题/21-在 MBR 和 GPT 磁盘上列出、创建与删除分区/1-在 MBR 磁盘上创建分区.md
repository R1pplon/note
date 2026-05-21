# 在 MBR 磁盘上创建分区

首先，你将使用传统的 MBR 分区方案。你的任务是使用 `fdisk` 工具在可用的块设备 `/dev/vdb` 上创建一个新的主分区。

## 要求

- 使用 `fdisk` 命令管理 `/dev/vdb` 磁盘上的分区。
- 创建一个大小为 500M 的新主分区。
- 创建完成后，验证新分区是否存在。

## 示例

最初，磁盘 `/dev/vdb` 没有分区表。在你创建新分区后，`sudo fdisk -l /dev/vdb` 的输出应该类似于下面这样，显示出新的 `/dev/vdb1` 分区。

```
Disk /dev/vdb: 40 GiB, 42949672960 bytes, 83886080 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x85191cd4

Device     Boot Start     End Sectors  Size Id Type
/dev/vdb1        2048 1026047 1024000  500M 83 Linux
```
