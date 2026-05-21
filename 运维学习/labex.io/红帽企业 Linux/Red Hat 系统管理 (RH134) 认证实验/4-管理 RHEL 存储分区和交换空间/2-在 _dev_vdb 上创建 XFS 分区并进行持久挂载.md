# 在 /dev/vdb 上创建 XFS 分区并进行持久挂载

在此步骤中，你将为 `/dev/vdb` 创建一个新分区，使用 XFS 文件系统进行格式化，并配置它以实现持久挂载。

你将在 `/dev/vdb` 上创建一个 1 GB 的主分区，并将文件系统类型指定为 XFS。为了获得最佳性能，将分区对齐到扇区边界是一个好习惯。从扇区 2048 开始是一个常见的对齐方式。

**首先，你需要在未初始化的磁盘上创建一个分区表。** 使用 `parted` 的交互模式来创建分区表和分区：

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

**注意：** `mklabel msdos` 命令会在磁盘上创建一个 MBR (Master Boot Record) 分区表。在创建任何分区之前都需要它。创建分区表后，你可以继续使用 `mkpart` 来创建实际的分区。由于分区从扇区 2048 开始，将结束位置设置为 `1001MB` 将会创建一个大约 1 GB 的分区。当你退出 parted 时，你会看到关于更新 `/etc/fstab` 的信息提示，这是正常的。

要验证分区是否已创建，请使用 `parted` 打印 `/dev/vdb` 的分区表：

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

创建新分区后，告知内核这些更改至关重要。`udevadm settle` 命令会等待系统注册新分区并创建其对应的设备文件（例如 `/dev/vdb1`）。

```bash
udevadm settle
```

现在分区已创建，你需要使用 XFS 文件系统对其进行格式化。这会为分区准备好存储数据。使用 `mkfs.xfs` 命令进行此操作：

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

为了使文件系统可访问，你需要挂载它。首先，创建一个挂载点目录。你将把这个分区挂载到 `/archive`。

```bash
mkdir /archive
```

为了实现持久挂载（意味着文件系统会在每次系统启动时自动挂载），你需要向 `/etc/fstab` 文件添加一个条目。在 `/etc/fstab` 中使用分区的 Universally Unique Identifier (UUID) 是最佳实践，因为像 `/dev/vdb1` 这样的设备名称在添加或删除新磁盘时可能会发生变化。

使用 `lsblk --fs` 发现 `/dev/vdb1` 的 UUID：

```bash
lsblk --fs /dev/vdb1
```

记下输出中的 UUID。它看起来会像 `881e856c-37b1-41e3-b009-ad526e46d987`。

```plaintext
NAME FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
vdb1 xfs                2ee03827-6acf-4543-9a21-0fd031250b45
```

现在，使用 `nano` 打开 `/etc/fstab` 文件，并在文件末尾添加新的一行来配置你的分区。将 `YOUR_UUID_HERE` 替换为你刚刚找到的实际 UUID。

```bash
nano /etc/fstab
```

将以下行添加到文件末尾：

```text
UUID=YOUR_UUID_HERE /archive xfs defaults 0 0
```

**`/etc/fstab` 条目解释：**

- `UUID=YOUR_UUID_HERE`: 使用 UUID 指定要挂载的设备。
- `/archive`: 挂载点目录。
- `xfs`: 文件系统类型。
- `defaults`: 一组常用的挂载选项（rw, suid, dev, exec, auto, nouser, async）。
- `0`: `dump` 选项（0 表示不进行 dump）。
- `0`: `fsck` 顺序（0 表示启动时不对其进行 fsck 检查）。

通过按 `Ctrl+X`，然后按 `Y` 确认，最后按 `Enter` 将更改写入文件来保存文件。

修改 `/etc/fstab` 后，你需要通知 `systemd` 重新加载其配置，以便它识别新条目。

```bash
systemctl daemon-reload
```

最后，使用 `/etc/fstab` 中的条目挂载新文件系统。`mount /archive` 命令将使用 `/etc/fstab` 中的信息将 `/dev/vdb1` 挂载到 `/archive`。

```bash
mount /archive
```

通过检查 `mount` 命令的输出并过滤 `/archive` 来验证新文件系统是否已正确挂载：

```bash
mount | grep /archive
```

你应该会看到类似以下的输出，确认挂载成功：

```plaintext
/dev/vdb1 on /archive type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
```
