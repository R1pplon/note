# 使用 `parted` 和 `pvcreate` 准备物理卷

在这一步中，你将开始管理存储，为逻辑卷管理器（LVM）准备物理磁盘。这涉及两个关键阶段：首先，使用 `parted` 实用程序对磁盘进行分区，其次，使用 `pvcreate` 命令将这些分区初始化为 LVM 物理卷（PV）。

**逻辑卷管理器（LVM）概述**

LVM 是 Linux 中一个强大的存储管理工具，它在物理存储设备之上提供了一个灵活的层。LVM 允许你从一个或多个物理设备创建抽象的“卷组”，然后从该池化的空间中划分出“逻辑卷”，而不是直接使用磁盘和分区。这使得调整存储大小、更换磁盘以及管理系统的存储变得更加容易，而无需停机。

LVM 中最基本的组件是**物理卷（PV）**。PV 是一个物理存储设备，例如硬盘分区或整个磁盘，它已被初始化以供 LVM 使用。

### 1. 为 LVM 创建分区

在 LVM 可以使用磁盘之前，你必须在磁盘上创建一个分区并将其类型设置为“LVM”。我们将使用 `/dev/vdb` 设备进行此练习。你将需要 `sudo` 权限来修改磁盘分区。

首先，在 `/dev/vdb` 设备上创建一个新的 GUID 分区表（GPT）。GPT 是物理存储设备上分区表布局的现代标准。

```bash
sudo parted /dev/vdb mklabel gpt
```

接下来，创建一个大小为 512 MiB 的单个分区。我们将此分区命名为 `lvm-part1`。

```bash
sudo parted /dev/vdb mkpart lvm-part1 1MiB 513MiB
```

现在，将分区类型设置为 `lvm`。此标志告诉系统此分区旨在与逻辑卷管理器一起使用。

```bash
sudo parted /dev/vdb set 1 lvm on
```

为了确保内核立即识别新分区，请运行 `udevadm settle` 命令。此命令等待 `udev` 守护程序处理所有设备事件，确保新分区 `/dev/vdb1` 可用。

```bash
sudo udevadm settle
```

最后，通过打印分区表来验证分区是否已正确创建。

```bash
sudo parted /dev/vdb print
```

你应该看到类似于以下内容的输出，显示一个启用了 `lvm` 标志的分区。

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End    Size   File system  Name       Flags
 1      1049kB  538MB  537MB               lvm-part1  lvm
```

### 2. 将分区初始化为物理卷

创建分区并正确键入分区后，下一步是使用 `pvcreate` 命令将其初始化为 LVM 物理卷。此命令将 LVM 元数据写入分区，正式使其成为 LVM 系统的一部分。

运行以下命令以初始化 `/dev/vdb1`：

```bash
sudo pvcreate /dev/vdb1
```

成功的操作将产生以下消息：

```plaintext
  Physical volume "/dev/vdb1" successfully created.
  Creating devices file /etc/lvm/devices/system.devices
```

### 3. 显示物理卷信息

你现在可以检查新创建的物理卷。 `pvs` 命令提供了系统中所有 PV 的简要摘要，而 `pvdisplay` 提供了更详细的视图。

使用 `pvs` 查看快速摘要：

```bash
sudo pvs
```

输出将列出你的新 PV。请注意，它尚不属于任何卷组（VG）。

```plaintext
  PV         VG Fmt  Attr PSize   PFree
  /dev/vdb1     lvm2 ---  512.00m 512.00m
```

有关更多详细信息，请使用 `pvdisplay`：

```bash
sudo pvdisplay /dev/vdb1
```

此命令显示详细信息，包括 PV 名称、大小和唯一标识符（UUID）。

```plaintext
  "/dev/vdb1" is a new physical volume of "512.00 MiB"
  --- NEW Physical volume ---
  PV Name               /dev/vdb1
  VG Name
  PV Size               512.00 MiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

你现在已经成功地为 LVM 准备了一个物理分区。在下一步中，你将使用此 PV 创建一个卷组。
