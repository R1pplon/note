## 介绍

在这个实验中，你将学习在 Red Hat Enterprise Linux 系统上创建和管理 LVM（逻辑卷管理器）存储的基本过程。你将从准备物理磁盘开始，使用 `parted` 对其进行分区，并使用 `pvcreate` 将其初始化为 LVM 物理卷（PV）。接下来，你将使用 `vgcreate` 将这个 PV 汇集到一个卷组（VG）中，然后使用 `lvcreate` 从该组创建一个灵活的逻辑卷（LV）。初始设置以使用 XFS 文件系统格式化新的 LV 并将其持久挂载以供系统使用而结束。

在初始配置的基础上，你将探索 LVM 的关键优势之一：动态调整存储大小的能力。你将学习如何通过使用 `vgextend` 添加新的物理卷来扩展现有的卷组。随后，你将使用 `lvextend` 扩展逻辑卷以利用这个新空间，并使用 `xfs_growfs` 在线调整 XFS 文件系统的大小，以便在不宕机的情况下立即向操作系统提供额外的容量。

## 使用 `parted` 和 `pvcreate` 准备物理卷

在这一步中，你将开始管理存储，为逻辑卷管理器（LVM）准备物理磁盘。这涉及两个关键阶段：首先，使用 `parted` 实用程序对磁盘进行分区，其次，使用 `pvcreate` 命令将这些分区初始化为 LVM 物理卷（PV）。

**逻辑卷管理器（LVM）概述**

LVM 是 Linux 中一个强大的存储管理工具，它在物理存储设备之上提供了一个灵活的层。LVM 允许你从一个或多个物理设备创建抽象的“卷组”，然后从该池化的空间中划分出“逻辑卷”，而不是直接使用磁盘和分区。这使得调整存储大小、更换磁盘以及管理系统的存储变得更加容易，而无需停机。

LVM 中最基本的组件是**物理卷（PV）**。PV 是一个物理存储设备，例如硬盘分区或整个磁盘，它已被初始化以供 LVM 使用。

### 1. 为 LVM 创建分区

在 LVM 可以使用磁盘之前，你必须在磁盘上创建一个分区并将其类型设置为“LVM”。我们将使用 `/dev/vdb` 设备进行此练习。你将需要 `sudo` 权限来修改磁盘分区。

首先，在 `/dev/vdb` 设备上创建一个新的 GUID 分区表（GPT）。GPT 是物理存储设备上分区表布局的现代标准。

```bash
sudo parted /dev/vdb mklabel gpt
```

接下来，创建一个大小为 512 MiB 的单个分区。我们将此分区命名为 `lvm-part1`。

```bash
sudo parted /dev/vdb mkpart lvm-part1 1MiB 513MiB
```

现在，将分区类型设置为 `lvm`。此标志告诉系统此分区旨在与逻辑卷管理器一起使用。

```bash
sudo parted /dev/vdb set 1 lvm on
```

为了确保内核立即识别新分区，请运行 `udevadm settle` 命令。此命令等待 `udev` 守护程序处理所有设备事件，确保新分区 `/dev/vdb1` 可用。

```bash
sudo udevadm settle
```

最后，通过打印分区表来验证分区是否已正确创建。

```bash
sudo parted /dev/vdb print
```

你应该看到类似于以下内容的输出，显示一个启用了 `lvm` 标志的分区。

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

创建分区并正确键入分区后，下一步是使用 `pvcreate` 命令将其初始化为 LVM 物理卷。此命令将 LVM 元数据写入分区，正式使其成为 LVM 系统的一部分。

运行以下命令以初始化 `/dev/vdb1`：

```bash
sudo pvcreate /dev/vdb1
```

成功的操作将产生以下消息：

```plaintext
  Physical volume "/dev/vdb1" successfully created.
  Creating devices file /etc/lvm/devices/system.devices
```

### 3. 显示物理卷信息

你现在可以检查新创建的物理卷。 `pvs` 命令提供了系统中所有 PV 的简要摘要，而 `pvdisplay` 提供了更详细的视图。

使用 `pvs` 查看快速摘要：

```bash
sudo pvs
```

输出将列出你的新 PV。请注意，它尚不属于任何卷组（VG）。

```plaintext
  PV         VG Fmt  Attr PSize   PFree
  /dev/vdb1     lvm2 ---  512.00m 512.00m
```

有关更多详细信息，请使用 `pvdisplay`：

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

## 使用 `vgcreate` 从物理卷创建卷组

在这一步中，你将使用你在上一步中准备的物理卷（PV）来创建卷组（VG）。卷组是 LVM 的一个核心组件，它充当一个单一的、可管理的磁盘空间池，你可以从中创建逻辑卷。

**了解卷组和物理扩展**

**卷组（VG）** 将一个或多个物理卷聚合到一个存储池中。可以将其想象成将几个较小的水容器组合成一个大水箱。这种抽象是 LVM 具有灵活性的原因。

此存储池被划分为称为**物理扩展（PE）** 的小的、固定大小的块。默认情况下，PE 为 4 MiB。稍后创建逻辑卷时，你本质上是从卷组中分配一定数量的这些 PE。

### 1. 创建卷组

现在，你将使用 `/dev/vdb1` 物理卷创建一个名为 `my_vg` 的卷组。相应的命令是 `vgcreate`。

```bash
sudo vgcreate my_vg /dev/vdb1
```

如果命令成功，你将看到一条确认消息：

```plaintext
  Volume group "my_vg" successfully created
```

此命令创建了一个名为 `my_vg` 的新存储池，其中包含来自 `/dev/vdb1` 的所有可用空间。

### 2. 显示卷组信息

就像你对物理卷所做的那样，你可以显示有关新卷组的信息。 `vgs` 命令提供了一个摘要，而 `vgdisplay` 提供了详细的视图。

要获取系统中所有卷组的简洁摘要，请运行：

```bash
sudo vgs
```

输出将显示你的新 VG、其大小以及可用空间的数量。

```plaintext
  VG    #PV #LV #SN Attr   VSize   VFree
  my_vg   1   0   0 wz--n- 508.00m 508.00m
```

要获取有关你的特定卷组的更详细报告，请使用 `vgdisplay`：

```bash
sudo vgdisplay my_vg
```

此输出提供了全面的详细信息，包括 PE 大小、PE 总数以及仍有多少 PE 剩余。

```plaintext
  --- Volume group ---
  VG Name               my_vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               508.00 MiB
  PE Size               4.00 MiB
  Total PE              127
  Alloc PE / Size       0 / 0
  Free  PE / Size       127 / 508.00 MiB
  VG UUID               xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

请注意，`PE Size` 为 4.00 MiB，并且你有 127 个 `Free PE`，这对应于卷组中的总可用空间。你现在已经成功创建了一个卷组，并且已准备好从中创建逻辑卷。

## 使用 `lvcreate` 和 `mkfs.xfs` 创建并格式化逻辑卷

在这一步中，你将从你创建的卷组中划分出一个可用的块设备，称为逻辑卷（LV）。一旦创建了 LV，它仍然只是原始存储空间。要在其上存储文件，你必须使用文件系统对其进行格式化。

**了解逻辑卷和文件系统**

**逻辑卷（LV）** 相当于传统的磁盘分区。它从卷组中的可用空间创建，并作为标准块设备呈现给操作系统。你可以在 LV 上创建文件系统，挂载它们，并使用它们来存储数据。关键优势在于可以轻松调整 LV 的大小，这对于物理分区来说要困难得多。

**文件系统** 是操作系统用来控制数据存储和检索方式的数据结构。它将设备（如 LV）的原始空间组织成文件和目录。对于本实验，我们将使用 **XFS**，它是一个高性能的、日志式文件系统，是 Red Hat Enterprise Linux 的默认文件系统。

### 1. 创建逻辑卷

你现在将从 `my_vg` 卷组创建一个名为 `my_lv` 的 256 MiB 逻辑卷。相应的命令是 `lvcreate`。

- `-n my_lv`：指定新 LV 的名称。
- `-L 256M`：将 LV 的大小设置为 256 兆字节。
- `my_vg`：要从中创建 LV 的卷组的名称。

执行以下命令：

```bash
sudo lvcreate -n my_lv -L 256M my_vg
```

成功的命令将产生此输出：

```plaintext
  Logical volume "my_lv" created.
```

### 2. 验证逻辑卷的创建

你可以使用 `lvs` 和 `lvdisplay` 命令检查你的新 LV。

要查看所有 LV 的摘要，请运行：

```bash
sudo lvs
```

输出将显示你的新 LV `my_lv`，位于 `my_vg` 组中。

```plaintext
  LV    VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  my_lv my_vg -wi-a----- 256.00m
```

对于详细视图，你可以指定 LV 的完整路径。LV 的路径通常是 `/dev/VG_NAME/LV_NAME`。

```bash
sudo lvdisplay /dev/my_vg/my_lv
```

这提供了详细信息，包括 LV 路径，你将在下一步中需要它。

```plaintext
  --- Logical volume ---
  LV Path                /dev/my_vg/my_lv
  LV Name                my_lv
  VG Name                my_vg
  LV UUID                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  LV Write Access        read/write
  LV Creation host, time host, 2023-10-27 10:30:00 +0000
  LV Status              available
  # open                 0
  LV Size                256.00 MiB
  Current LE             64
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:2
```

### 3. 使用 XFS 文件系统格式化逻辑卷

现在，你将使用 `mkfs.xfs` 命令使用 XFS 文件系统格式化 `my_lv` 逻辑卷。这将准备卷以存储文件。

```bash
sudo mkfs.xfs /dev/my_vg/my_lv
```

该命令将输出有关它正在创建的文件系统的详细信息。

```plaintext
meta-data=/dev/my_vg/my_lv       isize=512    agcount=4, agsize=16384 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1
data     =                       bsize=4096   blocks=65536, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

你的逻辑卷现在已格式化，可以挂载和使用。你将在下一步中执行此操作。

## 使用 `/etc/fstab` 持久挂载逻辑卷

在这一步中，你将使你创建和格式化的逻辑卷对系统可用。这涉及两个操作：创建一个目录作为“挂载点”，然后配置系统，以便在每次启动时自动将逻辑卷附加到此目录。

**了解挂载和 `/etc/fstab`**

一个已格式化的块设备，例如你的 `my_lv` 逻辑卷，在它被**挂载**之前不能直接使用。挂载是将文件系统附加到主文件系统树中的特定目录的过程。挂载后，你可以进入该目录以读取和写入设备上的文件。

为了确保在重新启动后自动挂载文件系统，你必须在 `/etc/fstab`（文件系统表）文件中为其添加一个条目。此文件包含系统应在启动时挂载的所有磁盘和分区的列表。

### 1. 创建挂载点

挂载点只是一个空目录，它将充当你的新文件系统的根目录。我们将为此目的创建一个名为 `/data` 的目录。

```bash
sudo mkdir /data
```

### 2. 在 `/etc/fstab` 中添加一个条目以进行持久挂载

现在，你需要告诉系统自动将你的 LV 挂载到 `/data` 目录。你将向 `/etc/fstab` 文件添加一个新行。此文件中的每一行都有六个字段：

1. **设备（Device）**：要挂载的设备。在我们的例子中，是 `/dev/my_vg/my_lv`。
2. **挂载点（Mount Point）**：要挂载到的目录。这里是 `/data`。
3. **文件系统类型（Filesystem Type）**：文件系统的类型，即 `xfs`。
4. **挂载选项（Mount Options）**：用于挂载的选项。 `defaults` 是一组适用于大多数情况的标准选项。
5. **转储（Dump）**：由 `dump` 实用程序使用，以决定是否备份文件系统。 `0` 表示禁用。
6. **传递（Pass）**：由 `fsck` 使用，以确定在启动时检查文件系统的顺序。 `0` 表示不检查。

我们将使用 `echo` 命令与 `sudo tee -a` 结合使用，将正确的行追加到 `/etc/fstab`，而无需文本编辑器。

```bash
echo '/dev/my_vg/my_lv /data xfs defaults 0 0' | sudo tee -a /etc/fstab
```

你可以通过查看文件的内容来验证该行是否已正确添加：

```bash
cat /etc/fstab
```

### 3. 挂载文件系统

现在 `/etc/fstab` 中存在该条目，你可以使用 `mount` 命令立即挂载它。因为挂载点 `/data` 在 `/etc/fstab` 中列出，所以你只需要提供目录名称。

```bash
sudo mount /data
```

系统将读取 `/etc/fstab`，找到 `/data` 的条目，并挂载相应的设备。

### 4. 验证挂载

要确认文件系统已成功挂载，你可以使用带有 `-h`（人类可读）选项的 `df`（磁盘空闲）命令。

```bash
df -h /data
```

输出应显示你的逻辑卷挂载在 `/data` 上，并显示其总大小和可用空间。

```plaintext
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/my_vg-my_lv     251M   28M  224M  11% /data
```

你还可以创建一个测试文件以确保你具有写入权限：

```bash
echo "My LVM is working!" | sudo tee /data/test.txt
```

然后，读回该文件以确认它已被写入：

```bash
cat /data/test.txt
```

```plaintext
My LVM is working!
```

你的逻辑卷现在已挂载，并且将在每次系统启动时自动可用。

## 使用 `vgextend` 和 `lvextend` 扩展卷组和逻辑卷

在这一步中，你将体验 LVM 最强大的功能之一：动态增加存储空间的能力。你将向你现有的卷组添加一个新的物理磁盘分区以扩展其总容量，然后扩展你的逻辑卷以使用一些新空间。

**了解存储扩展**

使用 LVM 的主要原因之一是其灵活性。当你空间不足时，你不需要执行复杂的数据迁移。相反，你可以简单地向卷组添加另一个物理磁盘（或分区），然后根据需要增长你的逻辑卷。所有这些都可以在线完成，无需卸载文件系统。

- `vgextend`：此命令将一个或多个物理卷添加到现有的卷组，从而增加其总大小。
- `lvextend`：此命令增加逻辑卷的大小，从其卷组内的空闲扩展中提取空间。

### 1. 准备新的物理卷

要扩展卷组，你首先需要一个新的物理卷。我们将在 `/dev/vdb` 设备上创建一个第二个分区，就像你在第一步中所做的那样。

首先，创建一个新的 512 MiB 分区。我们将其放置在第一个分区之后。

```bash
sudo parted /dev/vdb mkpart lvm-part2 513MiB 1025MiB
```

接下来，将分区类型设置为 `lvm`。

```bash
sudo parted /dev/vdb set 2 lvm on
```

确保内核识别新的分区 `/dev/vdb2`。

```bash
sudo udevadm settle
```

最后，将此新分区初始化为物理卷。

```bash
sudo pvcreate /dev/vdb2
```

你应该看到成功消息：

```plaintext
  Physical volume "/dev/vdb2" successfully created.
```

### 2. 扩展卷组

现在，使用 `vgextend` 命令将新的物理卷（`/dev/vdb2`）添加到你现有的卷组（`my_vg`）。

```bash
sudo vgextend my_vg /dev/vdb2
```

确认消息将指示成功：

```plaintext
  Volume group "my_vg" successfully extended
```

你可以使用 `vgs` 命令验证更改。注意 `VSize` 和 `VFree` 已经显着增加。

```bash
sudo vgs my_vg
```

```plaintext
  VG    #PV #LV #SN Attr   VSize    VFree
  my_vg   2   1   0 wz--n- 1022.00m 766.00m
```

`my_vg` 卷组现在跨越两个物理分区，并且有更多可用空间。

### 3. 扩展逻辑卷

由于卷组中有更多可用空间，你现在可以扩展你的逻辑卷。让我们将 `my_lv` 的大小从 256 MiB 增加到新的总大小 400 MiB。

带有 `-L` 选项的 `lvextend` 命令设置卷的新的绝对大小。

```bash
sudo lvextend -L 400M /dev/my_vg/my_lv
```

输出将确认调整大小操作。

```plaintext
  Size of logical volume my_vg/my_lv changed from 256.00 MiB (64 extents) to 400.00 MiB (100 extents).
  Logical volume my_vg/my_lv successfully resized.
```

使用 `lvs` 验证逻辑卷的新大小：

```bash
sudo lvs /dev/my_vg/my_lv
```

```plaintext
  LV    VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  my_lv my_vg -wi-ao---- 400.00m
```

**重要提示：** 你已成功扩展了逻辑卷（块设备），但驻留在其上的 XFS 文件系统尚未意识到这个新空间。如果你检查已挂载文件系统的大小，它仍将报告旧的大小。

```bash
df -h /data
```

```plaintext
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/my_vg-my_lv     251M   28M  224M  11% /data
```

在下一步中，你将调整文件系统的大小以填充逻辑卷中新可用的空间。

## 使用 `xfs_growfs` 调整 XFS 文件系统大小

在最后一步中，你将通过调整 XFS 文件系统的大小来完成存储扩展过程，使其能够识别你为其底层逻辑卷分配的额外空间。这是使新空间可用于存储文件的关键最后一步。

**了解文件系统调整大小**

当你扩展逻辑卷时，你只是在增加容器（块设备）的大小。该容器_内_的文件系统仍保持其原始大小，并且不知道设备末尾的新未使用空间。

对于 XFS 文件系统，`xfs_growfs` 命令用于扩展文件系统以填充底层设备。XFS 的一个主要优点是，此操作可以在线执行，同时文件系统已挂载并正在使用，无需停机时间。

### 1. 扩展 XFS 文件系统

`xfs_growfs` 命令以文件系统的挂载点作为其参数。在你的例子中，挂载点是 `/data`。

运行以下命令以扩展文件系统：

```bash
sudo xfs_growfs /data
```

该命令将输出有关更改的信息，指示数据块的旧数量和新数量。

```plaintext
meta-data=/dev/mapper/my_vg-my_lv isize=512    agcount=4, agsize=16384 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1
data     =                       bsize=4096   blocks=65536, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 65536 to 102400
```

关键行是 `data blocks changed from 65536 to 102400`，它确认文件系统已增长。

### 2. 验证新的文件系统大小

现在，再次运行 `df -h` 命令以验证文件系统大小是否反映了更改。

```bash
df -h /data
```

输出现在应显示新的、更大的大小，大约为 400 MiB。

```plaintext
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/my_vg-my_lv     395M   29M  367M   8% /data
```

你还可以检查你之前创建的测试文件是否仍然完好无损：

```bash
cat /data/test.txt
```

```plaintext
My LVM is working!
```

恭喜你！你已成功完成了整个 LVM 工作流程：从对物理磁盘进行分区和创建物理卷，到构建卷组、创建逻辑卷、格式化和挂载它，最后，在没有任何数据丢失的情况下动态地在线扩展它。

## 总结

在这个实验中，你学习了使用 RHEL 中的逻辑卷管理器（LVM）设置存储的基本工作流程。你首先准备了一个物理磁盘，使用 `parted` 创建了一个设置了 `lvm` 标志的分区，然后使用 `pvcreate` 将其初始化为物理卷（PV）。之后，你使用 `vgcreate` 将 PV 汇集到一个新的卷组（VG）中。从这个 VG 中，你使用 `lvcreate` 划分出一个逻辑卷（LV），使用 `mkfs.xfs` 格式化它，并将其配置为通过向 `/etc/fstab` 添加一个条目来实现持久挂载。

该实验还通过指导你完成扩展现有逻辑卷的过程，展示了 LVM 的灵活性。这包括准备一个新的物理卷，使用 `vgextend` 将其添加到卷组以增加总可用空间，然后使用 `lvextend` 扩展逻辑卷本身。最后，你使用 `xfs_growfs` 在线调整 XFS 文件系统的大小，使额外的存储空间可供操作系统使用，从而完成了动态存储管理的端到端过程。