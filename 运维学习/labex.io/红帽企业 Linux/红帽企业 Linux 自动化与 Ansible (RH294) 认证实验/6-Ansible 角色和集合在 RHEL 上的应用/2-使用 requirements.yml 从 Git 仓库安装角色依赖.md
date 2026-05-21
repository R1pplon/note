# 使用 requirements.yml 从 Git 仓库安装角色依赖

在本步骤中，你将学习如何管理来自外部源（如 Git 仓库）的角色依赖项。这在大型 Ansible 项目中是一种常见做法，你可以在其中重用社区或其他团队开发的角色。Ansible 使用一个文件（通常命名为 `requirements.yml`）来定义要安装的角色列表。

你的自定义角色 `apache.developer_configs` 将依赖于一个基础的 Apache 角色，以确保 Web 服务器已安装并运行。你将定义此依赖项并进行安装。

首先，请确保你位于主项目目录中。如果你仍然在上一 B 步骤的 `roles` 子目录中，请导航回 `~/project`。

```bash
cd ~/project
```

现在，你将在 `roles` 目录中创建 `requirements.yml` 文件。此文件将列出你的项目所需的所有外部角色。使用 `nano` 编辑器来创建和编辑该文件。

```bash
nano roles/requirements.yml
```

将以下内容添加到文件中。此条目指示 `ansible-galaxy` 从公共 Git 仓库下载特定版本的 Apache 角色，并在本地将其命名为 `infra.apache`。

```yaml
- name: infra.apache
  src: https://github.com/geerlingguy/ansible-role-apache.git
  scm: git
  version: 3.2.0
```

让我们来分解一下这个定义：

- `name`: 这是角色的本地名称。即使源仓库名称不同，它也会被安装到名为 `infra.apache` 的目录中。
- `src`: Git 仓库的源 URL。
- `scm`: 指定源代码管理工具，本例中为 `git`。
- `version`: 要使用的特定 Git 分支、标签或提交哈希。固定版本对于确保你的自动化稳定且可预测至关重要。

保存文件并退出 `nano`，按 `Ctrl+X`，然后按 `Y`，最后按 `Enter`。

在 `requirements.yml` 文件就位后，你现在可以使用 `ansible-galaxy install` 命令下载并安装该角色。

- `-r` 标志指向你的 requirements 文件。
- `-p` 标志指定角色应安装的路径。

```bash
ansible-galaxy install -r roles/requirements.yml -p roles
```

你将看到输出确认下载和安装过程。

```plaintext
Starting galaxy role install process
- downloading role 'ansible-role-apache', owned by geerlingguy
- downloading role from https://github.com/geerlingguy/ansible-role-apache/archive/3.2.0.tar.gz
- extracting infra.apache to /home/labex/project/roles/infra.apache
- infra.apache (3.2.0) was installed successfully
```

为了确认角色已正确安装，请列出 `roles` 目录的内容。

```bash
ls -l roles
```

现在你应该会看到 `infra.apache` 目录以及你之前创建的 `apache.developer_configs` 角色。

```plaintext
total 12
drwxr-xr-x. 9 labex labex 4096 Nov 10 10:10 apache.developer_configs
drwxr-xr-x. 9 labex labex 4096 Nov 10 10:15 infra.apache
-rw-r--r--. 1 labex labex  118 Nov 10 10:12 requirements.yml
```

你现在已成功将外部 Git 仓库声明为依赖项并将其安装到你的项目中。下一步是将此依赖项集成到你的自定义角色的元数据中。
