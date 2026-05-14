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

## 在 /dev/vdb 上创建并激活 swap 分区



## 在 /dev/vdb 上创建额外的分区
## 格式化第三个分区并持久挂载
## 在 /dev/vdb 上创建带有优先级的额外 swap 分区
## 验证持久挂载配置（无需重启）
## 总结