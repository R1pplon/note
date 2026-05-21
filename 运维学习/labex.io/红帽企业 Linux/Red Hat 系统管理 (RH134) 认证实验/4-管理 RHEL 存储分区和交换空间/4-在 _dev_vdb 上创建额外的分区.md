# 在 /dev/vdb 上创建额外的分区

在这一步中，你将在 `/dev/vdb` 上创建额外的分区。由于你已经使用 MBR (msdos) 分区表创建了一个 XFS 分区和一个交换分区，你仍然有空间可以创建更多的分区。现在，你将创建一个第三个分区，它将演示如何管理更大的分区。

首先，让我们检查 `/dev/vdb` 上的当前分区表和可用空间：

```bash
parted /dev/vdb print
```

你应该看到你之前创建的两个分区：

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary  xfs
 2      1001MB  1501MB  500MB   primary  linux-swap
```

现在，你将创建一个 2 GB 的第三个分区，用于额外的存储空间。此分区将从 `1501MB`（交换分区的末尾）开始，到 `3501MB`（1501MB + 2000MB）结束。

```bash
parted /dev/vdb mkpart primary xfs 1501MB 3501MB
```

你可能会看到 `Information: You may need to update /etc/fstab.` 消息。

验证第三个分区的创建：

```bash
parted /dev/vdb print
```

你现在应该看到三个分区：

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary  xfs
 2      1001MB  1501MB  500MB   primary  linux-swap
 3      1501MB  3501MB  2000MB  primary
```

运行 `udevadm settle` 以确保系统检测到新分区：

```bash
udevadm settle
```
