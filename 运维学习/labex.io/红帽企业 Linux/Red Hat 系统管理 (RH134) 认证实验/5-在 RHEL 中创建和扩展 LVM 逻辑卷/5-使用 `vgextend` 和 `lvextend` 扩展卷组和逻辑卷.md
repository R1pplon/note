# 使用 `vgextend` 和 `lvextend` 扩展卷组和逻辑卷

在这一步中，你将体验 LVM 最强大的功能之一：动态增加存储空间的能力。你将向你现有的卷组添加一个新的物理磁盘分区以扩展其总容量，然后扩展你的逻辑卷以使用一些新空间。

**了解存储扩展**

使用 LVM 的主要原因之一是其灵活性。当你空间不足时，你不需要执行复杂的数据迁移。相反，你可以简单地向卷组添加另一个物理磁盘（或分区），然后根据需要增长你的逻辑卷。所有这些都可以在线完成，无需卸载文件系统。

- `vgextend`：此命令将一个或多个物理卷添加到现有的卷组，从而增加其总大小。
- `lvextend`：此命令增加逻辑卷的大小，从其卷组内的空闲扩展中提取空间。

### 1. 准备新的物理卷

要扩展卷组，你首先需要一个新的物理卷。我们将在 `/dev/vdb` 设备上创建一个第二个分区，就像你在第一步中所做的那样。

首先，创建一个新的 512 MiB 分区。我们将其放置在第一个分区之后。

```bash
sudo parted /dev/vdb mkpart lvm-part2 513MiB 1025MiB
```

接下来，将分区类型设置为 `lvm`。

```bash
sudo parted /dev/vdb set 2 lvm on
```

确保内核识别新的分区 `/dev/vdb2`。

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

现在，使用 `vgextend` 命令将新的物理卷（`/dev/vdb2`）添加到你现有的卷组（`my_vg`）。

```bash
sudo vgextend my_vg /dev/vdb2
```

确认消息将指示成功：

```plaintext
  Volume group "my_vg" successfully extended
```

你可以使用 `vgs` 命令验证更改。注意 `VSize` 和 `VFree` 已经显着增加。

```bash
sudo vgs my_vg
```

```plaintext
  VG    #PV #LV #SN Attr   VSize    VFree
  my_vg   2   1   0 wz--n- 1022.00m 766.00m
```

`my_vg` 卷组现在跨越两个物理分区，并且有更多可用空间。

### 3. 扩展逻辑卷

由于卷组中有更多可用空间，你现在可以扩展你的逻辑卷。让我们将 `my_lv` 的大小从 256 MiB 增加到新的总大小 400 MiB。

带有 `-L` 选项的 `lvextend` 命令设置卷的新的绝对大小。

```bash
sudo lvextend -L 400M /dev/my_vg/my_lv
```

输出将确认调整大小操作。

```plaintext
  Size of logical volume my_vg/my_lv changed from 256.00 MiB (64 extents) to 400.00 MiB (100 extents).
  Logical volume my_vg/my_lv successfully resized.
```

使用 `lvs` 验证逻辑卷的新大小：

```bash
sudo lvs /dev/my_vg/my_lv
```

```plaintext
  LV    VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  my_lv my_vg -wi-ao---- 400.00m
```

**重要提示：** 你已成功扩展了逻辑卷（块设备），但驻留在其上的 XFS 文件系统尚未意识到这个新空间。如果你检查已挂载文件系统的大小，它仍将报告旧的大小。

```bash
df -h /data
```

```plaintext
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/my_vg-my_lv     251M   28M  224M  11% /data
```

在下一步中，你将调整文件系统的大小以填充逻辑卷中新可用的空间。
