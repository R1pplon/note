# 使用 rd.break 和 chroot 重置 Root 密码

在这一步中，你将学习在 RHEL 系统上重置丢失的根密码的程序。这是一项关键的恢复技能。标准方法涉及使用 `rd.break` 内核参数中断启动过程，这使你可以在系统完全启动之前访问 shell。

在物理机或虚拟机上，你将重新启动，中断 GRUB 引导加载程序，并将 `rd.break` 添加到 `linux` 内核行的末尾。此操作会在 `systemd` 接管控制之前停止启动过程，将你置于 `initramfs` shell 中。从那里开始，一般步骤是：

1. 使用命令 `mount -o remount,rw /sysroot` 以读写模式重新挂载系统的根文件系统（它以只读模式挂载在 `/sysroot`）。
2. 使用 `chroot /sysroot` 在 `/sysroot` 处进入 `chroot` jail。这将使系统的实际根文件系统成为你当前的环境，允许你运行影响系统的命令。
3. 使用 `passwd` 命令更改密码。
4. 解决潜在的 SELinux 上下文问题。
5. 退出 `chroot` 和 `initramfs` shell 以继续启动。

虽然我们无法在此实验环境中执行真正的重启并使用 `rd.break`，但我们将模拟在进入 `chroot` 环境后将要执行的最重要的命令。

首先，让我们模拟更改根密码。假设你已成功进入 `chroot` jail。你现在将拥有 root 权限来更改任何用户的密码。我们将使用 `sudo passwd root` 命令来更改 `root` 用户的密码。当提示时，将新密码设置为 `redhat`。

```bash
sudo passwd root
```

系统将提示你输入并重新输入新密码（例如 `labex.io`）。

```plaintext
Changing password for user root.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

在此恢复环境中更改密码后，密码文件（`/etc/shadow`）上的 SELinux 安全上下文可能会变得不正确。要修复此问题，你必须在下次启动时强制进行完整的系统 SELinux 重新标记。这通过在根目录（`/`）中创建一个名为 `.autorelabel` 的空文件来完成。

```bash
sudo touch /.autorelabel
```

让我们验证该文件是否已创建。

```bash
ls -l /.autorelabel
```

输出应显示新创建的文件。

```plaintext
-rw-r--r--. 1 root root 0 <date> <time> /.autorelabel
```

在真实系统中，你现在将键入两次 `exit` 并让系统重新启动。它将执行冗长的重新标记过程，然后使用新密码正常启动。由于我们不想在我们的实验中触发此操作，我们将通过删除我们刚刚创建的文件来进行清理。

```bash
sudo rm /.autorelabel
```

这结束了重置根密码的模拟。你已经练习了作为恢复过程核心的关键命令（`passwd` 和 `touch /.autorelabel`）。
