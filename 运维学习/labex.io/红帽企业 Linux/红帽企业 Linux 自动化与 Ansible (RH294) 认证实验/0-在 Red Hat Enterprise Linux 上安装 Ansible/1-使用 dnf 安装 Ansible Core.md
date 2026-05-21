# 使用 dnf 安装 Ansible Core

在这一步中，你将使用 `dnf` 包管理器安装 `ansible-core` 包。Ansible Core 提供了基本的 Ansible 引擎，包括 `ansible`、`ansible-playbook` 和其他用于自动化任务的核心命令行工具。

`dnf` (Dandified YUM) 包管理器是 Red Hat Enterprise Linux 上用于管理软件包的标准工具。由于安装软件需要管理权限，因此你必须使用 `sudo` 命令。

运行以下命令，使用自动确认来安装 Ansible Core：

```bash
sudo dnf install ansible-core -y
```

`-y` 标志会自动回答所有提示“是”，使安装过程非交互式。系统将下载并安装 `ansible-core` 及其 Python 依赖项，包括用于模板的 Jinja2 和用于 YAML 处理的 PyYAML。

你应该看到类似于以下的输出，显示包的解析和安装进度：

```plaintext
Updating Subscription Management repositories.
Last metadata expiration check: ...
Dependencies resolved.
================================================================================
 Package                  Arch   Version                Repository         Size
================================================================================
Installing:
 ansible-core             noarch 2.16.x-x.el9            rhel-9-appstream   xx M
Installing dependencies:
 python3-jinja2           noarch x.x.x-x.el9              rhel-9-appstream   xxx k
 python3-yaml             x86_64 x.x.x-x.el9              rhel-9-appstream   xxx k
 ...

Transaction Summary
================================================================================
Install  XX Packages

Complete!
```
