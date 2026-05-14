## 介绍

在这个实验中，你将获得在 Red Hat Enterprise Linux (RHEL) 系统上管理存储分区和交换空间的实践经验。你将学习如何创建并持久挂载 XFS 分区，以及配置和激活具有不同优先级的交换分区。该实验使用 LabEx 虚拟机环境，其中包含可用的存储设备，使你能够练习这些基本的系统管理技能。

你将首先检查可用的磁盘，然后继续创建和管理分区，包括在必要时设置 GPT 分区表。该实验强调确保持久挂载和交换激活，从而全面了解 RHEL 环境中的磁盘管理。

## 检查可用的存储设备

在这一步中，你将检查你的 LabEx 虚拟机上可用的存储设备。LabEx 环境提供了一个额外的存储设备，你可以用它来练习分区操作。

首先，切换到 root 用户以执行磁盘管理操作。你目前以具有 sudo 权限的 `labex` 用户身份登录。

```bash
sudo su -
```

现在，让我们使用 `lsblk` 命令检查系统上可用的块设备：

```bash
lsblk
```

你应该看到类似这样的输出，显示各种存储设备：

```plaintext
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
vda    253:0    0   40G  0 disk
├─vda1 253:1    0    1M  0 part
├─vda2 253:2    0  100M  0 part /boot/efi
└─vda3 253:3    0 39.9G  0 part /
vdb    253:16   0   40G  0 disk
```

在这个环境中，你可以访问一个额外的存储设备 `/dev/vdb`，它尚未分区，可以立即使用。让我们更仔细地检查这个设备。

使用带有 `-f` 选项的 `lsblk` 命令来显示 `/dev/vdb` 的文件系统信息：

```bash
lsblk -f /dev/vdb
```

你应该看到类似这样的输出，表明 `/dev/vdb` 是一块新的、未格式化的磁盘：

```plaintext
NAME FSTYPE FSVER LABEL UUID FSAVAIL FSUSE% MOUNTPOINTS
vdb
```

接下来，使用 `parted` 命令获取关于磁盘的更详细信息，包括它的分区表：

```bash
parted /dev/vdb print
```

输出应该显示 `/dev/vdb` 上还没有分区表：

```plaintext
Error: /dev/vdb: unrecognised disk label
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: unknown
Disk Flags:
```

这确认了 `/dev/vdb` 是一块准备好进行分区的新磁盘。对于尚未用分区表初始化的磁盘，错误消息是正常的。

## 在 /dev/vdb 上创建 XFS 分区并进行持久挂载

在此步骤中，你将为 `/dev/vdb` 创建一个新分区，使用 XFS 文件系统进行格式化，并配置它以实现持久挂载。

你将在 `/dev/vdb` 上创建一个 1 GB 的主分区，并将文件系统类型指定为 XFS。为了获得最佳性能，将分区对齐到扇区边界是一个好习惯。从扇区 2048 开始是一个常见的对齐方式。

**首先，你需要在未初始化的磁盘上创建一个分区表。** 使用 `parted` 的交互模式来创建分区表和分区：

```bash
parted /dev/vdb
```

```plaintext
GNU Parted 3.5
Using /dev/vdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) mklabel msdos
(parted) mkpart
Partition type?  primary/extended? primary
File system type?  [ext2]? xfs
Start? 2048s
End? 1001MB
(parted) quit
Information: You may need to update /etc/fstab.
```

**注意：** `mklabel msdos` 命令会在磁盘上创建一个 MBR (Master Boot Record) 分区表。在创建任何分区之前都需要它。创建分区表后，你可以继续使用 `mkpart` 来创建实际的分区。由于分区从扇区 2048 开始，将结束位置设置为 `1001MB` 将会创建一个大约 1 GB 的分区。当你退出 parted 时，你会看到关于更新 `/etc/fstab` 的信息提示，这是正常的。

要验证分区是否已创建，请使用 `parted` 打印 `/dev/vdb` 的分区表：

```bash
parted /dev/vdb print
```

你应该会看到类似以下的输出，显示你新创建的主分区：

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary
```

创建新分区后，告知内核这些更改至关重要。`udevadm settle` 命令会等待系统注册新分区并创建其对应的设备文件（例如 `/dev/vdb1`）。

```bash
udevadm settle
```

现在分区已创建，你需要使用 XFS 文件系统对其进行格式化。这会为分区准备好存储数据。使用 `mkfs.xfs` 命令进行此操作：

```bash
mkfs.xfs /dev/vdb1
```

输出将显示 XFS 文件系统创建的详细信息：

```plaintext
meta-data=/dev/vdb1              isize=512    agcount=4, agsize=61056 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=244224, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

为了使文件系统可访问，你需要挂载它。首先，创建一个挂载点目录。你将把这个分区挂载到 `/archive`。

```bash
mkdir /archive
```

为了实现持久挂载（意味着文件系统会在每次系统启动时自动挂载），你需要向 `/etc/fstab` 文件添加一个条目。在 `/etc/fstab` 中使用分区的 Universally Unique Identifier (UUID) 是最佳实践，因为像 `/dev/vdb1` 这样的设备名称在添加或删除新磁盘时可能会发生变化。

使用 `lsblk --fs` 发现 `/dev/vdb1` 的 UUID：

```bash
lsblk --fs /dev/vdb1
```

记下输出中的 UUID。它看起来会像 `881e856c-37b1-41e3-b009-ad526e46d987`。

```plaintext
NAME FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
vdb1 xfs                2ee03827-6acf-4543-9a21-0fd031250b45
```

现在，使用 `nano` 打开 `/etc/fstab` 文件，并在文件末尾添加新的一行来配置你的分区。将 `YOUR_UUID_HERE` 替换为你刚刚找到的实际 UUID。

```bash
nano /etc/fstab
```

将以下行添加到文件末尾：

```text
UUID=YOUR_UUID_HERE /archive xfs defaults 0 0
```

**`/etc/fstab` 条目解释：**

- `UUID=YOUR_UUID_HERE`: 使用 UUID 指定要挂载的设备。
- `/archive`: 挂载点目录。
- `xfs`: 文件系统类型。
- `defaults`: 一组常用的挂载选项（rw, suid, dev, exec, auto, nouser, async）。
- `0`: `dump` 选项（0 表示不进行 dump）。
- `0`: `fsck` 顺序（0 表示启动时不对其进行 fsck 检查）。

通过按 `Ctrl+X`，然后按 `Y` 确认，最后按 `Enter` 将更改写入文件来保存文件。

修改 `/etc/fstab` 后，你需要通知 `systemd` 重新加载其配置，以便它识别新条目。

```bash
systemctl daemon-reload
```

最后，使用 `/etc/fstab` 中的条目挂载新文件系统。`mount /archive` 命令将使用 `/etc/fstab` 中的信息将 `/dev/vdb1` 挂载到 `/archive`。

```bash
mount /archive
```

通过检查 `mount` 命令的输出并过滤 `/archive` 来验证新文件系统是否已正确挂载：

```bash
mount | grep /archive
```

你应该会看到类似以下的输出，确认挂载成功：

```plaintext
/dev/vdb1 on /archive type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
```

## 在 /dev/vdb 上创建并激活 swap 分区

在这一步中，你将在 `/dev/vdb` 磁盘上创建一个 swap 分区。Swap 空间是硬盘或固态硬盘上的一部分，当系统物理内存不足时，会将其用作临时存储。它充当 RAM 的溢出区，即使内存紧张，系统也能继续运行，但速度会变慢。

首先，检查 `/dev/vdb` 上当前的分区表，以确定在哪里创建新的 swap 分区。

```bash
parted /dev/vdb print
```

你应该能看到上一步创建的现有 XFS 分区 (`/dev/vdb1`)：

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 5369MB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary  xfs
```

现在，你需要添加一个 500 MB 的新主分区，用作 swap 空间。你会将该分区的文件系统类型设置为 `linux-swap`。新分区将紧接在现有 `/dev/vdb1` 分区之后开始。`/dev/vdb1` 的结束位置是 `1001MB`。因此，新分区将从 `1001MB` 开始，到 `1501MB`（1001MB + 500MB）结束。

使用非交互模式的 `parted` 来创建此分区：

```bash
parted /dev/vdb mkpart primary linux-swap 1001MB 1501MB
```

你可能会再次看到 `Information: You may need to update /etc/fstab.` 这条消息。

通过列出 `/dev/vdb` 磁盘上的分区来验证你的工作：

```bash
parted /dev/vdb print
```

现在你应该看到两个分区，第二个就是你新创建的 swap 分区：

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary  xfs
 2      1001MB  1501MB  499MB   primary               swap
```

和之前一样，创建新分区后，必须运行 `udevadm settle` 以确保系统识别新分区并创建设备文件（`/dev/vdb2`）。

```bash
udevadm settle
```

现在，使用 `mkswap` 命令将新分区 (`/dev/vdb2`) 格式化为 swap 空间。该命令会初始化分区以供 swap 使用。

```bash
mkswap /dev/vdb2
```

输出将显示 swap 空间创建的详细信息，包括其大小和生成的 UUID：

```plaintext
Setting up swapspace version 1, size = 476 MiB (499118080 bytes)
no label, UUID=4379b167-ab39-4c83-bf7c-b28fbdb38725
```

要配置新的 swap 空间持久激活，你需要在 `/etc/fstab` 文件中添加相应条目。首先，找到 `/dev/vdb2` 设备的 UUID。

```bash
lsblk -o UUID /dev/vdb2
```

记下输出中的 UUID。它类似于 `4379b167-ab39-4c83-bf7c-b28fbdb38725`。

```plaintext
UUID
4379b167-ab39-4c83-bf7c-b28fbdb38725
```

使用 `nano` 打开 `/etc/fstab` 文件，并为 swap 分区添加新行。请将 `YOUR_SWAP_UUID_HERE` 替换为你刚找到的实际 UUID。

```bash
nano /etc/fstab
```

将以下行添加到文件末尾：

```text
UUID=YOUR_SWAP_UUID_HERE swap swap defaults 0 0
```

**`/etc/fstab` 中 swap 条目的解释：**

- `UUID=YOUR_SWAP_UUID_HERE`：指定用作 swap 的设备。
- `swap`：挂载点（对于 swap，始终是 `swap`）。
- `swap`：文件系统类型（对于 swap，始终是 `swap`）。
- `defaults`：swap 的标准选项。
- `0`：`dump` 选项（0 表示不备份）。
- `0`：`fsck` 顺序（0 表示不对 swap 进行 fsck 检查）。

按 `Ctrl+X` 保存文件，然后按 `Y` 确认，再按 `Enter` 写入文件。

修改 `/etc/fstab` 后，重新加载 `systemd` 守护进程以识别新条目。

```bash
systemctl daemon-reload
```

最后，使用 `swapon -a` 命令启用 swap 空间。`-a` 选项告诉 `swapon` 启用 `/etc/fstab` 中列出的所有 swap 设备。

```bash
swapon -a
```

使用 `swapon --show` 验证新的 swap 空间是否已启用：

```bash
swapon --show
```

你应该会看到类似下面的输出，确认新的 swap 分区已激活：

```plaintext
NAME      TYPE      SIZE USED PRIO
/dev/vdb2 partition 476M   0B   -2
```

输出显示你新创建的 swap 分区已激活，随时可用。

## 在 /dev/vdb 上创建额外的分区

在这一步中，你将在 `/dev/vdb` 上创建额外的分区。由于你已经使用 MBR (msdos) 分区表创建了一个 XFS 分区和一个交换分区，你仍然有空间可以创建更多的分区。现在，你将创建一个第三个分区，它将演示如何管理更大的分区。

首先，让我们检查 `/dev/vdb` 上的当前分区表和可用空间：

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

现在，你将创建一个 2 GB 的第三个分区，用于额外的存储空间。此分区将从 `1501MB`（交换分区的末尾）开始，到 `3501MB`（1501MB + 2000MB）结束。

```bash
parted /dev/vdb mkpart primary xfs 1501MB 3501MB
```

你可能会看到 `Information: You may need to update /etc/fstab.` 消息。

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

运行 `udevadm settle` 以确保系统检测到新分区：

```bash
udevadm settle
```

## 格式化第三个分区并持久挂载

在这一步中，你将使用 XFS 文件系统格式化第三个分区 (`/dev/vdb3`)，并将其配置为在 `/backup` 处进行持久挂载。

首先，使用 XFS 文件系统格式化 `/dev/vdb3` 分区：

```bash
mkfs.xfs /dev/vdb3
```

输出将显示有关 XFS 文件系统创建的详细信息：

```plaintext
meta-data=/dev/vdb3              isize=512    agcount=4, agsize=122880 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1
data     =                       bsize=4096   blocks=491520, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

现在，为此分区创建一个挂载点目录。你将把它挂载到 `/backup`。

```bash
mkdir /backup
```

为了确保文件系统自动挂载，你需要向 `/etc/fstab` 添加一个条目。首先，找到 `/dev/vdb3` 分区的 UUID。

```bash
lsblk -o UUID /dev/vdb3
```

记下输出中的 UUID。它将是一个唯一的标识符，例如 `f74ed805-b1fc-401a-a5ee-140f97c6757d`。

```plaintext
UUID
f74ed805-b1fc-401a-a5ee-140f97c6757d
```

使用 `nano` 打开 `/etc/fstab` 文件并添加新条目。将 `YOUR_UUID_HERE` 替换为你找到的实际 UUID。

```bash
nano /etc/fstab
```

将以下行添加到文件的末尾：

```text
UUID=YOUR_UUID_HERE /backup xfs defaults 0 0
```

保存文件 (`Ctrl+X`，`Y`，`Enter`)。

修改 `/etc/fstab` 后，重新加载 `systemd` 守护程序以应用更改。

```bash
systemctl daemon-reload
```

最后，手动挂载 `/backup` 目录以验证配置是否正确。

```bash
mount /backup
```

通过检查 `mount` 命令的输出来确认挂载是否成功：

```bash
mount | grep /backup
```

你应该看到类似这样的输出：

```plaintext
/dev/vdb3 on /backup type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
```

## 在 /dev/vdb 上创建带有优先级的额外 swap 分区

在这一步中，你将在 `/dev/vdb` 上创建一个额外的交换分区，并了解分区表的限制。你还将学习如何为交换分区分配优先级。当有多个交换分区处于活动状态时，系统将首先使用优先级最高的分区。

**了解分区表的限制：**

当前的设置使用 MBR (msdos) 分区表，它仅限于 4 个主分区。由于你已经创建了 4 个分区，因此在转换为 GPT 或使用扩展分区之前，你无法创建额外的主分区。

首先，检查 `/dev/vdb` 上的当前分区表：

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

现在，创建第四个分区作为 512 MB 的交换分区。它将从 `3501MB`（第三个分区的末尾）开始，到 `4013MB`（3501MB + 512MB）结束。

```bash
parted /dev/vdb mkpart primary linux-swap 3501MB 4013MB
```

你可能会看到 `Information: You may need to update /etc/fstab.` 消息。

**关于 MBR 限制的说明：** 此时，你已达到 MBR 分区表的 4 分区限制。尝试创建第五个主分区将导致错误：`Error: Can't create any more partitions.`

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

运行 `udevadm settle` 以确保系统注册新分区并创建其设备文件 (`/dev/vdb4`)。

```bash
udevadm settle
```

现在，使用 `mkswap` 命令将新分区初始化为交换空间。记下 `/dev/vdb4` 的 UUID，因为你将在 `/etc/fstab` 中需要它。

```bash
mkswap /dev/vdb4
```

`/dev/vdb4` 的示例输出：

```plaintext
Setting up swapspace version 1, size = 488 MiB (511705088 bytes)
no label, UUID=87976166-4697-47b7-86d1-73a02f0fc803
```

要配置此交换空间以使用特定优先级激活，你需要向 `/etc/fstab` 文件添加一个条目。较高的 `pri`（优先级）值表示较高的偏好。你将为新的交换分区设置更高的优先级。

使用 `nano` 打开 `/etc/fstab`：

```bash
nano /etc/fstab
```

将以下行添加到文件的末尾，将 UUID 替换为你记下的那个：

```text
UUID=UUID_OF_VDB4   swap    swap  pri=10    0 0
```

**`pri` 选项的说明：**

- `pri=10`：为 `/dev/vdb4` 分配优先级 10。这高于 `/dev/vdb2` 的默认优先级 (-2)，因此系统将优先使用 `/dev/vdb4` 而不是 `/dev/vdb2`。

保存文件 (`Ctrl+X`，`Y`，`Enter`)。

重新加载 `systemd` 守护程序以识别新的 `/etc/fstab` 条目。

```bash
systemctl daemon-reload
```

使用 `swapon -a` 激活新的交换空间。

```bash
swapon -a
```

使用 `swapon --show` 验证交换空间的正确激活和优先级：

```bash
swapon --show
```

你应该看到输出，显示所有活动交换分区及其优先级。`/dev/vdb2` 将具有默认优先级 (-2)，而 `/dev/vdb4` 将具有你分配的优先级 (10)。

```plaintext
NAME      TYPE      SIZE USED PRIO
/dev/vdb2 partition 476M   0B   -2
/dev/vdb4 partition 488M   0B   10
```

**学习笔记：** 在生产环境中，如果你需要 4 个以上的分区，你将：

1. 转换为 GPT 分区表（支持多达 128 个分区）
2. 使用扩展分区及其内的逻辑分区
3. 使用 LVM (Logical Volume Manager) 进行更灵活的存储管理

## 验证持久挂载配置（无需重启）

在最后一步中，你将测试持久挂载配置，而无需实际重启系统，因为重启会让你与 LabEx 环境断开连接。相反，你将使用各种命令来模拟和验证你的配置在重启后是否能正确工作。

首先，让我们验证你的所有挂载条目是否在 `/etc/fstab` 中正确配置。显示 `/etc/fstab` 的内容以查看你的条目：

```bash
cat /etc/fstab
```

你应该看到你的 XFS 分区和交换空间的条目，类似于这样：

```plaintext
# ... existing system entries ...
UUID=your-vdb1-uuid /archive xfs defaults 0 0
UUID=your-vdb2-uuid swap swap defaults 0 0
UUID=your-vdb3-uuid /backup xfs defaults 0 0
UUID=your-vdb4-uuid swap    swap  pri=10    0 0
UUID=your-vdb5-uuid swap    swap  pri=20    0 0
```

现在，让我们通过卸载和重新挂载文件系统来测试挂载配置，以确保它们正常工作：

首先，卸载 `/archive` 目录：

```bash
umount /archive
```

验证它是否已卸载：

```bash
mount | grep /archive
```

这应该不返回任何输出。

现在，使用 `/etc/fstab` 条目重新挂载它：

```bash
mount /archive
```

再次验证它是否已挂载：

```bash
mount | grep /archive
```

你应该看到：

```plaintext
/dev/vdb1 on /archive type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
```

对 `/backup` 重复相同的过程：

```bash
umount /backup
mount /backup
mount | grep /backup
```

你应该看到：

```plaintext
/dev/vdb3 on /backup type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
```

对于交换空间，让我们通过关闭它们并再次打开它们来测试。首先，关闭所有交换：

```bash
swapoff -a
```

验证没有交换处于活动状态：

```bash
swapon --show
```

这应该只显示可能存在的任何系统交换，而不是你的自定义交换分区。

现在，使用 `/etc/fstab` 开启所有交换：

```bash
swapon -a
```

验证所有交换空间都处于活动状态并具有正确的优先级：

```bash
swapon --show
```

你应该看到类似这样的输出，其中所有交换分区都处于活动状态并具有正确的优先级：

```plaintext
NAME       TYPE       SIZE USED PRIO
/dev/vda2  partition    2G   0B   -2
/dev/vdb2  partition  476M   0B   -2
/dev/vdc2  partition  244M   0B   10
/dev/vdc3  partition  244M   0B   20
```

最后，让我们测试 `systemd` 是否可以处理你的所有 `/etc/fstab` 条目而没有错误：

```bash
systemctl daemon-reload
```

这应该在没有任何错误消息的情况下完成。

你还可以使用 `findmnt` 命令来验证内核是否能够挂载 `/etc/fstab` 中定义的所有文件系统：

```bash
findmnt --verify
```

此命令检查 `/etc/fstab` 中是否存在潜在问题，并且应该在没有错误的情况下完成。

显示你所有工作的最终摘要：

```bash
echo "=== Final Storage Configuration Summary ==="
echo "Partition tables:"
parted /dev/vdb print
echo ""
echo "Mounted filesystems:"
mount | grep -E "/archive|/backup"
echo ""
echo "Active swap spaces:"
swapon --show
echo ""
echo "fstab entries for persistence:"
grep -E "archive|backup|swap" /etc/fstab
echo ""
echo "UUID verification:"
echo "Device UUIDs:"
lsblk -f /dev/vdb* | grep -E "vdb[1-4]"
```

这结束了关于管理存储分区和交换空间的实验。你已经成功地创建和配置了具有不同文件系统的多个分区，设置了持久挂载，并配置了具有优先级的交换空间，所有这些都无需重启系统。

## 总结

在这个实验中，参与者学习了如何在 LabEx VM 环境中，在 RHEL 9 系统上管理存储分区和交换空间。实验首先检查了可用的存储设备 (`/dev/vdb`)，并了解了其当前状态，然后继续进行分区任务。

参与者使用 `/dev/vdb`，创建了一个 MBR 分区表，然后创建了多个分区：一个用于挂载到 `/archive` 的 XFS 分区，一个交换分区，另一个用于挂载到 `/backup` 的 XFS 分区，以及一个带有优先级配置的额外交换分区。该实验还演示了 MBR 分区表的限制（4 个主分区限制），并提供了对替代方案（如 GPT）的见解，适用于需要更多分区的情况。

该实验的一个关键方面是通过正确的 `/etc/fstab` 条目确保持久配置，并在不需要系统重启的情况下测试配置（这将断开 LabEx 环境的连接）。该实验最后通过全面的验证程序来确认所有挂载和交换空间将正确激活，从而在实际的、基于云的学习环境中提供了关于基本 RHEL 存储管理技能的实践经验。