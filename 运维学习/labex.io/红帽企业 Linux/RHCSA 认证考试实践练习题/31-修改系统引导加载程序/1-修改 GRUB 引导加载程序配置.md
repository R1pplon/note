# 修改 GRUB 引导加载程序配置

你的任务是修改 GRUB 的主配置文件，以更改系统启动时默认进入的内核。这涉及编辑一个配置文件，然后运行命令将更改应用到引导菜单中。

## 任务

- 修改 `/etc/default/grub` 文件以设置新的默认内核。
- 重新生成 GRUB 配置文件以应用更改。

## 要求

- 使用 `nano` 等文本编辑器编辑 `/etc/default/grub` 文件。
- 找到 `GRUB_DEFAULT` 参数并将其值更改为 `2`。
- 修改文件后，通过运行 `sudo grub2-mkconfig -o /boot/grub2/grub.cfg` 重新生成 GRUB 配置文件。

## 提示

- 你需要 `sudo` 权限来编辑 `/etc/default/grub` 以及运行 `grub2-mkconfig`。
- `grub2-mkconfig` 命令会读取 `/etc/default/grub` 中的设置来生成主配置文件，该文件位于 `/boot/grub2/grub.cfg`。

**注意**：通常情况下，需要重启系统才能以新的默认内核启动。但在本实验环境中，请**不要**重启系统。LabEx 虚拟机是一次性的，重启会导致挑战进度丢失。
