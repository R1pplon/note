# 使用 ansible-galaxy init 创建自定义 Ansible 角色

在本步骤中，你将开始使用 `ansible-galaxy init` 命令为新的 Ansible 角色创建一个标准化的目录结构。Ansible 角色是构建可重用和有组织的自动化内容的基础概念。它们允许你将任务 (tasks)、处理程序 (handlers)、变量 (variables) 和其他组件打包成一个独立的、可移植的单元。使用标准结构是一种最佳实践，可以使你的自动化内容更易于理解、管理和共享。

首先，请确保你位于正确的当前工作目录。本实验的所有操作都将在 `~/project` 目录中进行。

```bash
cd ~/project
```

在创建角色之前，你需要确保已安装 Ansible 命令行工具。`ansible-core` 包提供了核心工具，包括 `ansible-galaxy`。

使用 `dnf` 包管理器安装 `ansible-core`。`-y` 标志会自动对任何确认提示回答“是”。

```bash
sudo dnf install -y ansible-core
```

你应该会看到输出，表明该软件包正在安装并且依赖项已得到解决。

```plaintext
...
Installed:
  ansible-core-2.16.x-1.el9.x86_64
  ...
Complete!
```

将所有项目角色组织在一个专用的 `roles` 目录中是一种常见的做法。现在创建这个目录。

```bash
mkdir roles
```

现在，导航到新创建的 `roles` 目录。你将在其中初始化你的新自定义角色。

```bash
cd roles
```

你现在将使用 `ansible-galaxy init` 命令为名为 `apache.developer_configs` 的角色创建一个骨架。此命令会自动生成一套标准的目录和文件，为你的角色开发提供一个干净的起点。

```bash
ansible-galaxy init apache.developer_configs
```

运行命令后，你将看到一条确认消息。

```plaintext
- Role apache.developer_configs was created successfully
```

要查看刚刚创建的结构，你可以使用 `ls -R` 命令，该命令会递归地列出目录及其所有子目录的内容。

```bash
ls -R apache.developer_configs
```

输出显示了 Ansible 角色的标准目录结构：

```plaintext
apache.developer_configs:
defaults  files  handlers  meta  README.md  tasks  templates  tests  vars

apache.developer_configs/defaults:
main.yml

apache.developer_configs/files:

apache.developer_configs/handlers:
main.yml

apache.developer_configs/meta:
main.yml

apache.developer_configs/tasks:
main.yml

apache.developer_configs/templates:

apache.developer_configs/tests:
inventory  test.yml

apache.developer_configs/vars:
main.yml
```

以下是其中最重要的目录的简要概述：

- `tasks`: 包含角色要执行的任务的主列表。
- `handlers`: 包含处理程序，这些处理程序是仅在被另一个任务通知时才运行的任务。
- `vars`: 包含角色的变量。
- `templates`: 包含使用 Jinja2 模板引擎的文件模板。
- `meta`: 包含角色的元数据，包括对其他角色的依赖项。

你现在已经成功创建了自定义 Ansible 角色的基本结构。在接下来的步骤中，你将填充这些目录的内容来配置 Web 服务器。
