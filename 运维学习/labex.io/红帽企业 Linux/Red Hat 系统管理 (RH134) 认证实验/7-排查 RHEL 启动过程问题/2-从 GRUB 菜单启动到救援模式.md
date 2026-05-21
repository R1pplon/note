# 从 GRUB 菜单启动到救援模式

在这一步中，你将学习 `rescue.target`，这是一个专为系统恢复设计的特殊 `systemd` 目标。在标准的 RHEL 系统上，你将通过重启、中断引导加载程序（GRUB）以及向内核的启动选项添加参数来访问此模式。这提供了一个单用户 shell，其中根文件系统已挂载，并且大多数服务已禁用，这非常适合故障排除。

虽然我们无法在此容器化的实验环境中执行真正的重启或访问 GRUB 菜单，但我们仍然可以探索救援模式的配置以了解其工作原理。

首先，让我们找到 `rescue.target` 的 `systemd` 单元文件。这些文件通常存储在 `/usr/lib/systemd/system/` 目录中。

```bash
ls -l /usr/lib/systemd/system/rescue.target
```

你将看到该文件及其权限和所有权被列出。

```plaintext
-rw-r--r--. 1 root root 500 Nov  1  2022 /usr/lib/systemd/system/rescue.target
```

现在，让我们检查此文件的内容以了解其配置。`cat` 命令将在终端中显示文件的内容。

```bash
cat /usr/lib/systemd/system/rescue.target
```

输出显示了目标的定义。

```plaintext
#  SPDX-License-Identifier: LGPL-2.1-or-later
#
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Rescue Mode
Documentation=man:systemd.special(7)
Requires=sysinit.target rescue.service
After=sysinit.target rescue.service
AllowIsolate=yes
```

此文件中的关键指令包括：

- `Description=Rescue Mode`：目标的易于理解的名称。
- `Requires=sysinit.target rescue.service`：这确保在激活此目标时，`sysinit.target`（基本系统初始化）和 `rescue.service` 都已启动。救援服务提供根维护 shell。
- `After=sysinit.target rescue.service`：这指定了激活顺序，确保救援模式在系统初始化和救援服务之后启动。
- `AllowIsolate=yes`：这允许你使用运行系统中的 `systemctl isolate rescue.target` 命令从另一个目标切换到此目标。

为了更好地了解救援模式提供的最小环境，你可以查看其依赖项。`systemctl list-dependencies` 命令显示作为目标一部分启动的所有单元。

```bash
systemctl list-dependencies rescue.target
```

输出列出了救援模式所需的单元。你将看到一组最少量的服务，确认它是一个为修复任务设计的精简环境。

```plaintext
rescue.target
○ ├─rescue.service
○ ├─systemd-update-utmp-runlevel.service
● └─sysinit.target
●   ├─dev-hugepages.mount
●   ├─dev-mqueue.mount
●   ├─dracut-shutdown.service
○   ├─iscsi-onboot.service
○   ├─iscsi-starter.service
●   ├─kmod-static-nodes.service
●   ├─ldconfig.service
●   ├─lvm2-lvmpolld.socket
... (output may vary) ...
```

关键要点是 `rescue.target` 提供了一个根 shell，文件系统以读写方式挂载，使你能够修复系统问题。在后续步骤中，我们将模拟依赖于类似原则的恢复场景。
