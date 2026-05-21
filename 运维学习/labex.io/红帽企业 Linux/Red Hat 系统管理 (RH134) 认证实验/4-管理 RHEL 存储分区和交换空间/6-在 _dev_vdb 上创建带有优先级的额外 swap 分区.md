# 在 /dev/vdb 上创建带有优先级的额外 swap 分区

在这一步中，你将在 `/dev/vdb` 上创建一个额外的交换分区，并了解分区表的限制。你还将学习如何为交换分区分配优先级。当有多个交换分区处于活动状态时，系统将首先使用优先级最高的分区。

**了解分区表的限制：**

当前的设置使用 MBR (msdos) 分区表，它仅限于 4 个主分区。由于你已经创建了 4 个分区，因此在转换为 GPT 或使用扩展分区之前，你无法创建额外的主分区。

首先，检查 `/dev/vdb` 上的当前分区表：

```bash
parted /dev/vdb print
```

你应该看到你到目前为止创建的四个分区：

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary  xfs
 2      1001MB  1501MB  500MB   primary  linux-swap
 3      1501MB  3501MB  2000MB  primary  xfs
```

现在，创建第四个分区作为 512 MB 的交换分区。它将从 `3501MB`（第三个分区的末尾）开始，到 `4013MB`（3501MB + 512MB）结束。

```bash
parted /dev/vdb mkpart primary linux-swap 3501MB 4013MB
```

你可能会看到 `Information: You may need to update /etc/fstab.` 消息。

**关于 MBR 限制的说明：** 此时，你已达到 MBR 分区表的 4 分区限制。尝试创建第五个主分区将导致错误：`Error: Can't create any more partitions.`

显示分区表以验证你的工作：

```bash
parted /dev/vdb print
```

你现在应该看到四个分区：

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary  xfs
 2      1001MB  1501MB  500MB   primary  linux-swap
 3      1501MB  3501MB  2000MB  primary  xfs
 4      3501MB  4013MB  512MB   primary  linux-swap
```

运行 `udevadm settle` 以确保系统注册新分区并创建其设备文件 (`/dev/vdb4`)。

```bash
udevadm settle
```

现在，使用 `mkswap` 命令将新分区初始化为交换空间。记下 `/dev/vdb4` 的 UUID，因为你将在 `/etc/fstab` 中需要它。

```bash
mkswap /dev/vdb4
```

`/dev/vdb4` 的示例输出：

```plaintext
Setting up swapspace version 1, size = 488 MiB (511705088 bytes)
no label, UUID=87976166-4697-47b7-86d1-73a02f0fc803
```

要配置此交换空间以使用特定优先级激活，你需要向 `/etc/fstab` 文件添加一个条目。较高的 `pri`（优先级）值表示较高的偏好。你将为新的交换分区设置更高的优先级。

使用 `nano` 打开 `/etc/fstab`：

```bash
nano /etc/fstab
```

将以下行添加到文件的末尾，将 UUID 替换为你记下的那个：

```text
UUID=UUID_OF_VDB4   swap    swap  pri=10    0 0
```

**`pri` 选项的说明：**

- `pri=10`：为 `/dev/vdb4` 分配优先级 10。这高于 `/dev/vdb2` 的默认优先级 (-2)，因此系统将优先使用 `/dev/vdb4` 而不是 `/dev/vdb2`。

保存文件 (`Ctrl+X`，`Y`，`Enter`)。

重新加载 `systemd` 守护程序以识别新的 `/etc/fstab` 条目。

```bash
systemctl daemon-reload
```

使用 `swapon -a` 激活新的交换空间。

```bash
swapon -a
```

使用 `swapon --show` 验证交换空间的正确激活和优先级：

```bash
swapon --show
```

你应该看到输出，显示所有活动交换分区及其优先级。`/dev/vdb2` 将具有默认优先级 (-2)，而 `/dev/vdb4` 将具有你分配的优先级 (10)。

```plaintext
NAME      TYPE      SIZE USED PRIO
/dev/vdb2 partition 476M   0B   -2
/dev/vdb4 partition 488M   0B   10
```

**学习笔记：** 在生产环境中，如果你需要 4 个以上的分区，你将：

1. 转换为 GPT 分区表（支持多达 128 个分区）
2. 使用扩展分区及其内的逻辑分区
3. 使用 LVM (Logical Volume Manager) 进行更灵活的存储管理
