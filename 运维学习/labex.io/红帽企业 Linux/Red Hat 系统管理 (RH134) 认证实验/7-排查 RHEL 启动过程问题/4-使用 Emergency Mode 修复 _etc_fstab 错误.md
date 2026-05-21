# 使用 Emergency Mode 修复 /etc/fstab 错误

在这一步中，你将学习如何诊断和修复 `/etc/fstab` 文件中的错误。此文件对于启动过程至关重要，因为它告诉系统要挂载哪些文件系统以及挂载到哪里。`/etc/fstab` 中的条目不正确可能会阻止系统启动，迫使其进入“紧急模式”。

紧急模式为系统修复提供了尽可能最少的环境。与救援模式不同，它不会尝试挂载大多数文件系统或启动许多服务。至关重要的是，根文件系统（`/`）以只读（`ro`）模式挂载，以防止进一步损坏。

虽然我们无法在此实验中触发真正的启动失败，但我们可以模拟查找和修复 `/etc/fstab` 错误的过程。

首先，让我们有意向 `/etc/fstab` 添加一个错误的条目。我们将使用带有 `sudo` 的 `echo` 命令来追加引用不存在设备的行。

```bash
echo '/dev/nonexistent /data xfs defaults 0 0' | sudo tee -a /etc/fstab
```

现在，让我们查看 `/etc/fstab` 的内容以确认我们的错误行已添加。

```bash
cat /etc/fstab
```

你应该在文件的末尾看到不正确的行。

```plaintext
#
# /etc/fstab
# Created by anaconda on <date>
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/vda4 / xfs defaults 0 0
/dev/vda2 /boot xfs defaults 0 0
/dev/vda1 /boot/efi vfat umask=0077,shortname=winnt 0 0
/dev/vda3 swap swap defaults 0 0
/dev/nonexistent /data xfs defaults 0 0
```

接下来，我们将模拟诊断步骤。`mount -a` 命令尝试挂载 `/etc/fstab` 中列出的所有尚未挂载的文件系统。由于我们的条目无效，此命令将失败。

```bash
sudo mount -a
```

该命令将产生一个错误，清楚地表明挂载点 `/data` 不存在。这类似于你在启动失败期间看到的错误。

```plaintext
mount: /data: mount point does not exist.
```

现在，让我们模拟修复过程。在真正的紧急 shell 中，第一步是以读写模式重新挂载根文件系统以允许更改。

```bash
sudo mount -o remount,rw /
```

现在文件系统可写，你可以编辑 `/etc/fstab` 以修复错误。使用 `nano` 编辑器打开该文件。

```bash
sudo nano /etc/fstab
```

在 `nano` 编辑器中，使用箭头键导航到错误的行（`/dev/nonexistent /data xfs defaults 0 0`）并将其删除。你可以通过按 **Ctrl+k** 删除整行。删除该行后，通过按 **Ctrl+x**，然后按 **y**，最后按 **Enter** 来保存文件。

要确认修复，再次运行 `sudo mount -a`。

```bash
sudo mount -a
```

这次，该命令应该静默执行，没有输出，这表明 `/etc/fstab` 中的所有有效条目都已正确挂载。你已成功修复了该文件。
